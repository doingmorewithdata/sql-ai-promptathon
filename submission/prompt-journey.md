# Prompt Journey: Data Analyst Mission

## 1. Mission and starting objective

The mission was to identify a product-quality risk cluster for the Data Analyst role using evidence from the SQL database. The initial objective was to understand the data model, verify the core assumptions, and then test whether a specific product-quality issue was strong enough to justify prioritization.

## 2. Environment and tools used

The investigation used the SQL Server PromptathonDb database through SQL MCP tools. The workflow began with entity discovery and metadata inspection, then moved to targeted SQL queries for verification. The agent also used the database guide and the mission brief as contextual references.

## 3. Investigation phases

| Phase | Main prompt goal | SQL MCP tools used | Correction or insight |
|---|---|---|---|
| Baseline discovery | Describe available database entities and establish the likely data model | describe_entities | The named-entity metadata call failed, so the agent fell back to the full entity catalog and then used SQL queries to confirm the structure. |
| Model verification | Validate assumptions about row counts, keys, nulls, duplicates, and relationships | SQL MCP query tool; exact call not preserved in compacted chat | The initial assessment had inferred nullability and cardinality, so the agent verified those assumptions with actual SQL checks. |
| Sales baseline | Check whether the product-level sales picture was reliable | SQL MCP query tool; exact call not preserved in compacted chat | An early order-to-line comparison inflated header totals because of join grain; the comparison was rerun at order grain, and product sales were then based on SalesOrderLine.LineTotal. |
| Support baseline | Test whether support burden could be attributed at product level | SQL MCP query tool; exact call not preserved in compacted chat | Ticket attribution stayed conservative for multi-line orders and for tickets lacking RelatedSKU. |
| Complaint and qualitative review | Explore whether complaints converged around specific issues | SQL MCP query tool; exact call not preserved in compacted chat | Raw ticket count was challenged by calculating ticket rates; two other SKUs had higher rates, but only two tickets each. |
| Semantic evidence | Check whether related documents clustered around the same complaint theme | find_similar_docs_by_doc_id | The metric was initially described as similarity, but it was corrected to cosine distance, where lower values are closer. |
| Skeptical review | Stress-test the leading conclusion before finalizing it | SQL MCP query tool; exact call not preserved in compacted chat | SupportChat documents were recognized as derivative representations rather than additional independent complaints, and the target SKU’s heavy document seeding reduced confidence in claims about unbiased prevalence. |

## 4. Important agent struggles and corrections

The agent first described the database entities through SQL MCP, but the named-entity metadata call failed. It corrected course by switching to the full entity catalog and then proceeding with direct SQL queries. The agent also struggled with some queries and retried them until valid results were returned.

A more important correction involved the sales join logic. The first comparison between orders and lines inflated SalesOrder header totals because the join grain was too coarse. The agent detected the mismatch, reran the comparison at order grain, and stabilized the analysis on SalesOrderLine.LineTotal as the reliable line-level sales measure.

## 5. Key analytical decisions

Several decisions shaped the final conclusion:
- The analysis used product-level evidence from SalesOrderLine rather than header totals.
- Ticket attribution was conservative when the data was ambiguous.
- Ticket rates were used to avoid over-interpreting raw counts.
- Qualitative evidence was used to narrow the issue from broad complaint volume to a repeated pattern of wash-related smart-fabric connectivity failure.
- SupportChat records were treated as derivative evidence rather than additional independent complaints.

## 6. Final narrowed conclusion

The final conclusion was narrowed from a broad product-quality crisis to a specific SKU-level problem: SKU ZCPTM-SS-M-BW, Premium Short Sleeve Men's Top, showed a recurring wash/connectivity durability problem tied to smart-fabric behavior. The evidence was strongest at the SKU level and should not be generalized to the full Premium category.

## 7. Lessons learned

The investigation showed that evidence quality matters as much as raw signal volume. The agent learned to verify assumptions against the database rather than rely on inferred metadata, to watch join grain carefully, to compare rates as well as counts, and to distinguish independent complaints from derivative support representations. It also learned that seeded review content can make a dataset look more persuasive than its underlying evidence structure actually supports.
