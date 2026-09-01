## Retrieval-Model Decisions

### Decision 1: Embedding storage decision
Spec said: Incident chunks must store embeddings for vector similarity retrieval.
Schema decided: `embedding` is stored as `vector(1536) NOT NULL`.
Reason: The native vector type supports similarity search and the required HNSW index.

### Decision 2: ON DELETE decision
Spec said: `incident_chunks.incident_id` references `incidents.id`.
Schema decided: The foreign key uses `ON DELETE CASCADE`.
Reason: A chunk has no meaning after its parent incident is deleted.

### Decision 3: Filter-metadata / denormalisation decision
Spec said: Retrieval must support filtering by team and severity.
Schema decided: `team_id` and `severity` are stored directly on `incident_chunks`.
Reason: Denormalising these fields supports filtering during retrieval without requiring an additional join.

## Rejected Shape

### Shape: Embedding column on incidents
What it was: An `embedding` column stored directly on the `incidents` table.
Why rejected: One incident can contain multiple chunks, and each chunk requires its own embedding.
What would break: Long incident descriptions could not be represented by multiple independently searchable vectors.
