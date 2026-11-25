# AI Evaluation & Rubric Scoring API — Integration Guide

**Author:** Arafat Alalade  
**Version:** `v1.0` | **Base URL:** `https://api.eval-pipeline.example.com/v1`

Developer quickstart documentation, authentication standards, REST endpoint references, and working request/response payloads for an automated LLM response auditing and rubric scoring service.

---

## 1. Authentication & Headers
All API requests require a Bearer token passed in the `Authorization` header over HTTPS.

| Header | Value | Required |
| :--- | :--- | :--- |
| `Authorization` | `Bearer <YOUR_API_KEY>` | Yes |
| `Content-Type` | `application/json` | Yes |
| `X-Request-ID` | Client-generated UUID for idempotency and trace logging | Optional |

---

## 2. Quickstart: Submit an Evaluation Audit
Send a `POST` request to `/audits` with a prompt, two candidate model outputs, and active constraint rules.

### Endpoint
`POST /v1/audits`

### Request Body Parameters
- **`task_id`** (`string`, required): Unique identifier for the evaluation batch or prompt set.
- **`prompt`** (`string`, required): The exact user instruction given to the models.
- **`constraints`** (`object`, optional): Negative constraints and structural rules (`banned_terms`, `max_bullets`, `require_factual_correction`).
- **`responses`** (`array<object>`, required): Two or more candidate outputs (`id`, `text`) to compare.

### Example Request (`cURL`)
```bash
curl -X POST [https://api.eval-pipeline.example.com/v1/audits](https://api.eval-pipeline.example.com/v1/audits) \
  -H "Authorization: Bearer sk_live_98a7f6d5e4c3b2a1" \
  -H "Content-Type: application/json" \
  -d '{
    "task_id": "zk-rollup-summary-01",
    "prompt": "Explain how ZK rollups scale Ethereum in 3 short bullet points. Do not use the words blockchain, gas, or decentralized.",
    "constraints": {
      "banned_terms": ["blockchain", "gas", "decentralized"],
      "exact_bullets": 3
    },
    "responses": [
      {
        "id": "resp_a",
        "text": "- ZK rollups execute transactions off-chain and settle compressed batches on Ethereum mainnet.\n- Cryptographic validity proofs verify correctness without re-executing every transaction.\n- Batching transfers increases throughput and lowers per-transaction fees."
      },
      {
        "id": "resp_b",
        "text": "Sure! ZK rollups scale a blockchain by saving gas fees and keeping things decentralized."
      }
    ]
  }'
