# Kyverno — Admission Control

## Overview
Kyverno is a Kubernetes-native policy engine that validates, mutates, and
generates resources via admission webhooks. This module enforces pod-level
security constraints at admission time.

## Installation
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace

Verified state:
- Chart: kyverno-3.10.0, App: v1.20.0
- Namespace kyverno Active
- 4 controller pods Running (admission, background, cleanup, reports)

## Policy: disallow-root-user

Enforces that no container runs as UID 0. Uses the CEL-based
ValidatingPolicy API (policies.kyverno.io/v1), required by Kyverno v1.20+.

File: policies/disallow-root-vpol.yaml

## Test results

Violating Pod — rejected:
kubectl apply -f policies/test-root-pod.yaml
Error from server: admission webhook "vpol.validate.kyverno.svc-fail" denied the request

Compliant Pod — accepted:
kubectl apply -f policies/test-compliant-pod.yaml
pod/compliant-pod-test created

## Migration note
Kyverno v1.20 removed execution of legacy kyverno.io/v1 policy types.
The original ClusterPolicy manifest was rejected at admission; the policy
was rewritten as ValidatingPolicy (CEL).
