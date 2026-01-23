# Architectural Decision Records (ADR)


---


## Architectural Decision

An **Architecture Decision (AD)** is a choice that affects the structure, quality attributes, or behavior of a system. These decisions are driven by both **functional and non-functional requirements**.


---


## Decision Record

An **Architectural Decision Record (ADR)** is a document that describes a choice the team makes about a significant aspect of the software architecture. 

---

### Key Characteristics:
* **Description:** Captures the decision, its context, and its consequences.
* **Lifecycle:** (e.g., Proposed, Accepted, Rejected).
* **Decision Log:** The collection of ADRs forms a decision log.
* **Immutability:** To change a decision, a new ADR must be created to **supersede** the old one.

---


## ADR Contents

ADRs should follow a project-wide template to ensure consistency. At a minimum, every record must include:

1.  **Context:** What is the problem? What are the pressures or constraints?
2.  **Decision:** What is the proposed solution?
3.  **Consequences:** What are the trade-offs? What becomes easier or harder as a result?

---

## ADR Adoption Process

Every team member can create an ADR, but each record must have a designated **Owner**.

---

### The Review Workflow
1.  **Proposed:** The ADR is written and shared for review.
2.  **Review Meeting:** The team spends 10–15 minutes reading the ADR and flagging unclear topics.
3.  **Discussion:** The owner discusses comments with the team.
4.  **Outcome:** * **Accepted:** The owner adds a timestamp, version, and stakeholders.
    * **Rejected:** The owner records the reason for rejection to prevent future re-discussion.
    * **Rework:** If action points are identified, the ADR remains in the *Proposed* state until updated.



---


## Validation and Compliance

ADRs are used to validate that software changes conform to agreed-upon designs.

* **Peer Reviews:** During code reviews, reviewers check changes against the decision log. If code violates an ADR, the reviewer shares the ADR link, and the author must update the code to conform before merging.



---

## ADR Review and Updates

To maintain the integrity of the architecture, the following update rules apply:

* **Immutability:** Accepted or Rejected documents are never edited. A new ADR must be created. Once the new record is *Accepted*, the old one is marked as *Superseded* with a reference to the new version.



--

![Flow](assets/adr.png)

---

## Framework Goals

The ADR framework establishes a culture of:
* **Visibility:** Decisions are not hidden in private chats or emails.
* **Traceability:** The "why" behind a design is preserved for future developers.
* **Strategic Alignment:** Ensures teams, programs, and departments move in the same direction.


---


> ### Resources
> * **Architecture Decision Records:** [ADR GitHub Organization](https://adr.github.io/)
> * **Fundamentals of Software Architecture:** Richards, Mark and Neal Ford (2021). O'Reilly Media.§


---