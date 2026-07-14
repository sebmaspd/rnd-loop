# Product evaluation skill
Rename it to SKILL.md and place it at:

Global: `~/.claude/skills/product-evaluation/SKILL.md`  
Project-level: `your-project/.claude/skills/product-evaluation/SKILL.md`  
  
Then invoke with `/product-evaluation compare Snowflake vs Databricks vs Fabric` — the topic lands in $ARGUMENTS.  
Two things I'd flag about this design:  
Phase 0 will ask you questions.  
That's deliberate — it's the difference between a report that answers your actual decision and one that's just thorough.  
But if you want it to run unattended, add a line telling it to infer scope from $ARGUMENTS and state its assumptions instead of asking.  
The verification loop has no iteration cap, which means it can spin if a claim genuinely has no independent source anywhere.  
In practice this is fine — it'll downgrade or drop the claim.  
But if you find it stalling, add a cap of 3 with an escalation rule: "after 3 attempts, mark the claim [VENDOR-ONLY], label it in the report, and move on."  
