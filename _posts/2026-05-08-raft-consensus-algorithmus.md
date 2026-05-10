---
layout: default
title: "Konsens in verteilten Systemen mit dem Raft-Algorithmus"
description: "Technische Analyse von Raft: Von Leader Election und Log Replication bis hin zu Terms und Safety-Garantien in verteilten Systemen."
category: software-engineering
lang: de
permalink: /software-engineering/raft-consensus-algorithmus/
en_url: /en/software-engineering/raft-consensus-algorithm/
---

<style>
  .callout {
    border-left: 4px solid #b388ff;
    background: #15181e;
    border-radius: 0 8px 8px 0;
    padding: 1rem 1.2rem;
    margin: 1.5rem 0;
  }
  .callout strong { display: block; margin-bottom: 0.3rem; color: #b388ff; }
  
  .spec-box {
    background: #1e2229;
    border: 1px solid #30363d;
    border-radius: 8px;
    padding: 1rem;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.9rem;
    margin: 1.5rem 0;
  }
  .spec-title { color: #58a6ff; margin-bottom: 0.5rem; font-weight: bold; }
</style>

# Konsens in verteilten Systemen: Ein Deep Dive in den Raft-Algorithmus

In der modernen Software-Architektur ist "Single Point of Failure" ein Unwort. Wir bauen verteilte Systeme, um Ausfallsicherheit zu garantieren. Doch Redundanz allein reicht nicht aus – sie erzeugt das Problem der Konsistenz. Wie einigen sich fünf Instanzen von `etcd` in einem Kubernetes-Cluster darauf, welcher Wert für einen Key aktuell ist, wenn das Netzwerk instabil ist?

Die Antwort darauf ist der **Raft-Konsens-Algorithmus**. Er ist das Herzstück von Systemen wie CockroachDB, MongoDB und HashiCorp Vault.

## 1. Das Problem: Die Unzuverlässigkeit der physischen Welt

Netzwerke sind unzuverlässig (*Partitions*), Nachrichten gehen verloren oder kommen verzögert an. In einem Cluster ohne Koordination führt dies zum **Split-Brain-Szenario**.

<div class="callout">
<strong>Definition: Split-Brain</strong>
Ein Zustand, in dem ein verteiltes System aufgrund einer Netzwerkpartition in zwei oder mehr unabhängige Sub-Cluster zerfällt, die jeweils glauben, die alleinige Kontrolle über die Daten zu haben. Dies führt zwangsläufig zu Datenkorruption.
</div>

assets/images/software-engineering/raft-consensus-algorithmus
[Bild 1 - Adresse: /assets/images/software-engineering/raft-consensus-algorithmus/split-brain-partition.png]

Um dies zu verhindern, muss ein System **Linearisierbarkeit** garantieren: Es muss sich nach außen hin so verhalten, als gäbe es nur eine einzige, atomare Kopie der Daten.

## 2. Das Konzept der "Terms" (Amtszeiten)

Ein oft übersehenes, aber kritisches Element in Raft ist die Zeit. Raft unterteilt die Zeit in **Terms** (Amtszeiten) von beliebiger Länge. Terms werden durch fortlaufende ganze Zahlen nummeriert.

Jeder Term beginnt mit einer Wahl. Wenn ein Kandidat gewinnt, dient er für den Rest des Terms als Leader. Falls eine Wahl unentschieden ausgeht, beginnt sofort ein neuer Term mit einer neuen Wahl. Die Term-Nummer fungiert in Raft als **logische Uhr**. Wenn ein Knoten eine Nachricht von einem Leader mit einer niedrigeren Term-Nummer erhält, ignoriert er sie sofort – sie stammt von einem "veralteten" Anführer.

## 3. Leader Election: Wer darf führen?

Nur ein Leader darf Schreibanfragen entgegennehmen. Um die Wahl stabil zu halten, nutzt Raft zufällige **Election Timeouts**. 

Technisch gesehen sieht ein Wahl-Aufruf (RPC) in einer Go-ähnlichen Syntax so aus:

<div class="spec-box">
<div class="spec-title">// RequestVote RPC Argumente</div>
type RequestVoteArgs struct {<br>
&nbsp;&nbsp;Term         int // Term des Kandidaten<br>
&nbsp;&nbsp;CandidateId  int // ID des anfragenden Knotens<br>
&nbsp;&nbsp;LastLogIndex int // Index des letzten Log-Eintrags<br>
&nbsp;&nbsp;LastLogTerm  int // Term des letzten Log-Eintrags<br>
}
</div>

Ein Knoten gibt seine Stimme nur unter zwei Bedingungen ab:
1. Er hat in diesem Term noch nicht gewählt.
2. Das Log des Kandidaten ist **mindestens so aktuell** wie das eigene (Election Safety).

[Bild 2 - Adresse: /assets/images/software-engineering/raft-consensus-algorithmus/node-state-machine.png]

## 4. Log Replication & Quorum-Sicherheit

Der Leader diktiert die Wahrheit. Sobald ein Client eine Anfrage sendet, fügt der Leader diese seinem Log hinzu und schickt sie per `AppendEntries` an alle Follower.

<div class="callout">
<strong>Das Quorum-Prinzip</strong>
Ein Eintrag gilt erst dann als "Committed" (festgeschrieben), wenn er auf einer <strong>Mehrheit</strong> der Knoten (n/2 + 1) erfolgreich repliziert wurde. Bei 5 Knoten müssen also mindestens 3 Knoten den Erhalt bestätigen.
</div>

### Der Ablauf einer Replikation:

1. **Phase 1 (Uncommitted):** Der Leader sendet den Log-Eintrag. Follower speichern ihn, führen ihn aber noch nicht aus.
2. **Phase 2 (Commit):** Sobald das Quorum erreicht ist, erhöht der Leader seinen `commitIndex`. In der nächsten Nachricht erfahren die Follower davon und "committen" den Eintrag ebenfalls in ihre lokale State-Machine.

[Bild 3 - Adresse: /assets/images/software-engineering/raft-consensus-algorithmus/log-replication-flow.png]

## 5. Raft Safety: Die Leader Completeness Property

Warum ist Raft sicherer als einfache Mehrheitsentscheidungen? Die Antwort liegt in der **Leader Completeness Property**:
Raft garantiert, dass ein gewählter Leader alle Log-Einträge enthält, die in vorherigen Terms festgeschrieben (committed) wurden. 

Dies wird durch die Wahl-Regel sichergestellt: Ein Knoten mit einem veralteten Log kann niemals die Stimmen eines Knotens erhalten, der bereits einen committed Eintrag besitzt. Damit kann ein committed Eintrag niemals durch eine neue Wahl überschrieben werden.

## 6. Vergleich: Raft vs. Paxos vs. ZAB

| Feature | Raft | Paxos | ZAB (ZooKeeper) |
| :--- | :--- | :--- | :--- |
| **Verständlichkeit** | Hoch (Designziel) | Gering (Akademisch) | Mittel |
| **Struktur** | Starker Leader | Multi-Leader möglich | Leader-basiert |
| **Effizienz** | Hoch durch Batched Logs | Komplex zu optimieren | Sehr hoch |

## Fazit: Warum das für dich wichtig ist

Raft ist das Fundament der modernen Cloud. Ohne diesen Algorithmus gäbe es kein stabiles Kubernetes (via `etcd`) und kein skalierbares Kafka (via `KRaft`). Als Software Engineer ist das Verständnis von Konsens-Algorithmen der Schlüssel, um Systeme zu entwerfen, die nicht nur "funktioniert, wenn alles gut läuft", sondern die auch unter extremen Bedingungen (Netzwerkausfall, Hardwaredefekt) mathematisch beweisbar korrekt bleiben.

Die "Wahrheit" in einem verteilten System ist keine statische Gegebenheit – sie ist das Ergebnis einer kontinuierlichen demokratischen Abstimmung.
