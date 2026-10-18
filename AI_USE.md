# AI-use note

**Tools used.** Claude (Anthropic) was used to draft `WRITEUP.pdf`, build the results figure in
`figures/`, and draft `requirements.txt`. <!-- EDIT: add any other AI tools your team used (e.g. for the notebook code). -->

**One thing the AI got wrong.** While building the write-up PDF, the AI's first version silently lost the
superscript in "x^2" (the glyph did not render in the table), and two of its scripted text fixes failed
to apply without any error, so the PDF still contained the old wording. It also first wrote a speculative
explanation for the ready-made ZZ circuit's near-chance result that the README does not support.

**How we caught it.** We rendered the PDF pages to images and read them, which showed the missing
character and the unchanged text; we then compared the claims line by line against the README and removed
the unsupported explanation. <!-- EDIT: replace with your own example if you have a better one. -->
