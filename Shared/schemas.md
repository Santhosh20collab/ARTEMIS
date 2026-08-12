# Shared Data Schemas
Formats used to pass data between modules. Do not change these without discussing with the whole team.

## 1. Attack Log 
Every attack step the Pentester's scripts run gets logged as one JSON object per line (JSONL format).

File: `shared/logs/attack_log.jsonl`

{
  "timestamp": "2026-08-12T10:15:32Z",
  "step_name": "port_scan",
  "technique_id": "T1046",
  "source_ip": "192.168.56.101",
  "target_ip": "192.168.56.102",
  "status": "success"
}

Fields:
- timestamp — ISO 8601, UTC, when the step ran
- step_name — short human-readable name (e.g. port_scan, brute_force_ssh, exploit_ftp)
- technique_id — MITRE ATT&CK technique ID (e.g. T1046 = Network Service Discovery, T1110 = Brute Force)
- source_ip / target_ip — attacker and target VM IPs
- status — "success" or "failed"

## 2. Model Alert 
Every time the ML model flags traffic as anomalous, it logs one JSON object.

File: `shared/logs/model_alerts.jsonl`

{
  "timestamp": "2026-08-12T10:15:40Z",
  "source_ip": "192.168.56.101",
  "anomaly_score": 0.87,
  "is_anomaly": true,
  "model_version": "isolation_forest_v1"
}

Fields:
- timestamp — ISO 8601, UTC, when the model flagged this
- source_ip — IP the alert is about
- anomaly_score — raw model score (higher = more anomalous)
- is_anomaly — true/false after applying threshold
- model_version — which model produced this (helps compare Isolation Forest vs LSTM later)

## 3. Posture Score Log 
One entry per attack cycle run.

File: `shared/logs/posture_scores.jsonl`

{
  "cycle": 1,
  "timestamp": "2026-08-12T10:20:00Z",
  "detection_rate": 0.6,
  "fast_detection_rate": 0.4,
  "false_positive_rate": 0.1,
  "score": 0.58
}

## Notes
- All timestamps: ISO 8601, UTC, so everyone's logs line up regardless of machine.
- All logs: JSONL (one JSON object per line) — easy to append to, easy to parse with pandas (`pd.read_json(path, lines=True)`).
- If you need to add a field, open a PR and tag the other two — don't just add it silently, since it can silently break someone else's parser.
