# FAQ

**Do I need Python/Node/anything installed?** No. Just a browser and a text editor.

**Do I need the backend deployed first?** Yes. This tutorial creates a frontend that calls your already-deployed API. Follow the [main tutorial](/tutorials/partyrock-to-aws/tutorial.html) Steps 1–7 first.

**CORS error?** Your API Gateway or Lambda Function URLs need CORS headers. Ask Kiro to add them.

**403 Forbidden?** The API key in your HTML doesn't match the one in API Gateway. Check both.

**Can I use this offline?** No — it calls your cloud API. The AI runs on AWS, not your machine.

**How to change the design?** Edit index.html → save → refresh. Or ask Kiro.

**Is the API key safe in the HTML?** For personal use, fine. For public apps, add rate limiting (WAF) — see Step 9.3 of the main tutorial.
