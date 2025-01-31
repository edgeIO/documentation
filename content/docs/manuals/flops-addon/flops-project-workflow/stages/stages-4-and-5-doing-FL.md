---
title: "Stages 4 & 5: Doing FL"
summary: ""
draft: false
weight: 370
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
asciinema: true
---

## Stage 4: FL-Actors Deployment

The FL actors images stored in the FLOps image registry now get deployed on matching worker nodes.
Thanks to its rather light-weight computations the aggregator service can be deployed on an arbitrary worker node whereas learner services can only be deployed on dedicated notes that have the `FLOps-learner` addon enabled.
The aggregator image has a superset of the learner image dependencies thus pulling and deploying both actors take about the same amount of time.

Each actor service needs some time to spin up properly and connect to one another.
The learner services need to fetch and process their local training data before establishing a connection with their aggregator.


```bash
╭──────────────────────┬──────────────────────────┬────────────────┬──────────────────┬──────────────────────────╮
│ Service Name         │ Service ID               │ Instances      │ App Name         │ App ID                   │
├──────────────────────┼──────────────────────────┼────────────────┼──────────────────┼──────────────────────────┤
│                      │                          │                │                  │                          │
│ observ11e3c5ca7c4c   │ 679cbbaeaf4c1923eb5df1b4 │  0 RUNNING     │ observatory      │ 679cbbadaf4c1923eb5df1b2 │
│                      │                          │                │                  │                          │
├──────────────────────┼──────────────────────────┼────────────────┼──────────────────┼──────────────────────────┤
│                      │                          │                │                  │                          │
│ trackinge3afed0047b0 │ 679cbbaeaf4c1923eb5df1b5 │  0 RUNNING     │ observatory      │ 679cbbadaf4c1923eb5df1b2 │
│                      │                          │                │                  │                          │
├──────────────────────┼──────────────────────────┼────────────────┼──────────────────┼──────────────────────────┤
│                      │                          │                │                  │                          │
│ aggr11e3c5ca7c4c     │ 679cbbaeaf4c1923eb5df1b6 │  0 RUNNING     │ projc911185f81c4 │ 679cbbaeaf4c1923eb5df1b3 │
│                      │                          │                │                  │                          │
├──────────────────────┼──────────────────────────┼────────────────┼──────────────────┼──────────────────────────┤
│                      │                          │                │                  │                          │
│                      │                          │  0 RUNNING     │                  │                          │
│ flearner11e3c5ca7c4c │ 679cbbafaf4c1923eb5df1b7 │                │ projc911185f81c4 │ 679cbbaeaf4c1923eb5df1b3 │
│                      │                          │  1 RUNNING     │                  │                          │
│                      │                          │                │                  │                          │
╰──────────────────────┴──────────────────────────┴────────────────┴──────────────────┴──────────────────────────╯
```

## Stage 5: FL Training

The actual FL training takes place during this stage.
For our base-case scenario the processes are as described in the [FL Overview](/docs/concepts/flops/fl-basics/#federated-learning-overview).
Because the base-case should be as fast as possible it only trains for 3 rounds.
The following demo shows how a CLI user can inspect the running actors to observe the training rounds taking place.
At the end the aggregator sends the oberserver service the final/best achieved trained model metrics inlcuding accuracy and loss.
The aggregator notifies the FLOps manager which undeploys and removed the FL actors.

{{< asciinema key="flops_fl_training" poster="0:12" idleTimeLimit="1.5" >}}

### Observing FL Training via the GUI

Instead of looking into rather cryptic service logs you can and should observe this training process via a sophisticated GUI.
This GUI allows users to monitor, compare, store, export, share, and organize training runs, metrics, and trained models during training time and beyond.

{{<light-dark-png "gui-experiments-view-small">}}

FLOps uses [MLflow](https://mlflow.org/) to power its observability and tracking features.
The aggregator is augmented with mlflow to track its global training process after each training round.
The learners are not using mlflow to maximize privacy and minimize introducing further backdoors for attacks.


{{< link-card
  title="Want to know more about how FLOps uses MLflow for elevating its MLOps capabilities?"
  description="Learn how FLOps integrates MLflow into its architecture and workflows"
  href="TODO"
>}}
