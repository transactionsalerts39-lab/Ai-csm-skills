# Fiscal Calendar Contract

Use this calendar for every 6sense renewal, Clari, manager, weekly, Slack, Lattice, and command-centre workflow unless the user explicitly requests calendar quarters.

## Quarter Boundaries

- Q1: February 1 through April 30
- Q2: May 1 through July 31
- Q3: August 1 through October 31
- Q4: November 1 through January 31 of the following calendar year

January belongs to Q4 of the fiscal year that began in the previous calendar year.

Examples:

- May 31, 2026 → Q2 FY2026
- August 15, 2026 → Q3 FY2026
- January 27, 2027 → Q4 FY2026
- February 1, 2027 → Q1 FY2027

Compute labels dynamically from the date. Do not hardcode only the current year's ranges.

Use Asia/Kolkata for relative dates unless the source explicitly provides another timezone. Preserve the customer's timezone when quoting meeting availability or deadlines.

If the requested report historically expects a short label, use `Q2 26`; otherwise prefer `Q2 FY2026`.
