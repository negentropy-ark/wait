{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://github.com/negentropy-ark/wait/schemas/review-result.schema.json",
  "title": "WAIT Epistemic Review Result",
  "description": "Machine-readable output contract for the WAIT v0.2 Interrupt-Driven Epistemic & Anti-Labeling Review Agent.",
  "type": "object",
  "required": [
    "review_status",
    "severity",
    "review_scope",
    "epistemic_breakdown",
    "primary_risk",
    "recommended_action",
    "rationale"
  ],
  "additionalProperties": false,
  "properties": {
    "review_status": {
      "type": "string",
      "enum": ["PASS", "FLAG", "INTERRUPT"],
      "description": "Primary routing decision for the orchestrator."
    },
    "severity": {
      "type": "string",
      "enum": ["LOW", "MEDIUM", "HIGH", "CRITICAL"],
      "description": "Impact severity of the detected epistemic drift."
    },
    "review_scope": {
      "type": "string",
      "minLength": 1,
      "description": "Specific artifact, handoff payload, or reasoning transition under review."
    },
    "epistemic_breakdown": {
      "type": "object",
      "required": ["facts", "assumptions", "material_unknowns"],
      "additionalProperties": false,
      "properties": {
        "facts": {
          "type": "array",
          "items": { "type": "string" },
          "description": "Claims directly established by evidence in the review context ([FACT])."
        },
        "supported_inferences": {
          "type": "array",
          "items": { "type": "string" },
          "default": [],
          "description": "Reasonably derived claims not explicitly stated ([SUPPORTED_INFERENCE])."
        },
        "tentative_interpretations": {
          "type": "array",
          "items": { "type": "string" },
          "default": [],
          "description": "Plausible interpretations with material uncertainty ([TENTATIVE])."
        },
        "assumptions": {
          "type": "array",
          "items": { "type": "string" },
          "description": "Unverified premises or stereotype labels currently used operationally ([ASSUMPTION])."
        },
        "material_unknowns": {
          "type": "array",
          "items": { "type": "string" },
          "description": "Missing information relevant to downstream decisions ([UNKNOWN])."
        }
      }
    },
    "primary_risk": {
      "type": "string",
      "enum": [
        "W_UNSUPPORTED_LEAP",
        "A_ASSUMPTION_CONTAMINATION",
        "I_IDENTITY_OVERREACH",
        "T_TUNNEL_VISION",
        "NONE"
      ],
      "description": "Dominant W-A-I-T failure mode identified during review."
    },
    "interrupt_type": {
      "type": ["string", "null"],
      "enum": [
        "UNSUPPORTED_LEAP",
        "ASSUMPTION_CONTAMINATION",
        "IDENTITY_OVERREACH",
        "PREMATURE_CONVERGENCE",
        "EVIDENCE_CONFLICT",
        "MISSING_CRITICAL_INFORMATION",
        "HIGH_IMPACT_UNCERTAINTY",
        null
      ],
      "description": "Populated when review_status is INTERRUPT."
    },
    "earliest_unjustified_step": {
      "type": ["string", "null"],
      "description": "Earliest transition where certainty exceeded available evidence (TRACE target)."
    },
    "affected_downstream_stages": {
      "type": "array",
      "items": { "type": "string" },
      "default": [],
      "description": "Workflow nodes or agent stages contaminated by the unsupported assumption."
    },
    "recommended_action": {
      "type": "string",
      "enum": [
        "PASS",
        "SOFT_RELABEL",
        "SURGICAL_PRUNE",
        "SPLIT",
        "ASK",
        "BRANCH_REWIND"
      ],
      "description": "Minimum valid micro-repair or routing action."
    },
    "retry_constraint": {
      "type": ["object", "null"],
      "additionalProperties": false,
      "description": "Negative Constraint Guard injected during SURGICAL_PRUNE or BRANCH_REWIND to prevent stereotype relapse.",
      "properties": {
        "do_not_assume": {
          "type": "array",
          "items": { "type": "string" }
        },
        "treat_as_unknown": {
          "type": "array",
          "items": { "type": "string" }
        },
        "preserve_alternatives": {
          "type": "array",
          "items": { "type": "string" }
        }
      }
    },
    "rollback_target": {
      "type": ["string", "null"],
      "description": "Specific upstream node or state checkpoint to rewind to."
    },
    "resume_condition": {
      "type": ["string", "null"],
      "description": "Explicit condition required before downstream execution may safely resume."
    },
    "rationale": {
      "type": "string",
      "minLength": 1,
      "description": "Concise evidence-based explanation of the review decision."
    }
  }
}