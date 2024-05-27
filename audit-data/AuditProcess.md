# Audit Process

1. Read docs, know the business logic of this project
2. Use **solidity metrics** to get the scope of project, **place into sheet** organized by **size**. Make a status line to keep track. **Remove the files you don't need to audit!!!**
3. Start **taking notes** in the code, keep asking "how can I break it"
2. Run the static analysis "slither" & "aderyn" and copy the log into `Slither_Report.md`, `Aderyn_Report.md` (python -m slither . --exclude solc-version --filter-paths node_modules/ --filter-paths src/interfaces/ )(aderyn --exclude node_modules/)
3. Manual check one by one and make comment in this format(solidity visual developer plugin):
- `@audit` for security **review** or to start a discussion about potential security issues. For example, `@audit - Check for integer overflow. + [migrate]`
- `@audit-ok` for reviewed and deemed secure. For example: `@audit-ok - Integer overflow checked and safe`
- `@audit-info` for additional informational add or fix. For example: `@audit-info - This function can be called externally.`
Quick scan the obvious vulnerable one.
Go deep to business logics.
4. Copy paste the finding in `findings.md` in formatted.
Manage all the findings in the following format in `findings.md`: \
   **Severity:**\
    **Probability:**

```md
(H: HIGH, M:MIDIUM, L:LOW)
[H-#] TITLE
Description:

Impact:

Proof of Concept(Show the test code and output log):

Recommended Mitigation(show the diff of fixed code):
```
5. Check if there are any common bugs and optimization(gas, etc)(create a common bugs and optimization file).
6. Check the static analysis to make it perfect
7. Move on to the next file
8. Write good `findings.md` and copy paste on audit platform
8. If needed and time allowed, using **chatGPT** or **Gemini** AI to check again. Copy all the code in `ProjectSummary.md` and send to gemini (large token)
9. Using chatGPT to format and fix the grammar mistakes for `findings.md`
10. Generate Report by using **aderyn** (Private auditing)
