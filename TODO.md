# TODO (v4 candidates)

## Known deviations from the ideal spec
- The subtitle frequency list ships pre-lowercased, so the ">80% capitalised
  in subtitle occurrences" proper-noun heuristic could not be applied from
  that source; proper-noun exclusion relies on Wiktionary's `pos=name` tag
  instead.
- Part of speech is the word's most frequent POS in the tagged corpus. A
  lemma has at most two entries: a second POS needs 20% of the lemma's
  corpus tokens and a distinct sense. The articles il/un also sit beside
  their pronoun/numeral homographs. Same-spelling pairs (come prep/conj)
  make the engine validator print a "share surface form" warning; that is
  expected.
- The spaCy Italian model is CC BY-NC-SA 3.0, not MIT. The pack ships no
  model files; the model is used only at build time. This project is
  non-commercial.

Residuals from the v3 QA rounds. See `tools/REPORT.md` for the rules
already in place and their counts.

## Sentence links
- "Dai," at the start of a sentence (come on) links to the preposition da.
- The conjunction che is sometimes tagged PRON and links to che (pron).
- Compounds are read word by word. "fine settimana" (weekend) links nothing
  useful, and "come se" (as if) links come "how". A compound table or
  multi-word links would fix both.
- Second-entry link swaps. Some sentences go to the other entry of the same
  lemma: via noun vs adv, ufficiale adj vs noun, and cosa noun getting
  pronoun sentences. The tagger's POS decides, so its errors show here.

## Words and glosses
- The piano adverb ("slowly, quietly") is absent. It is about 2% of piano
  tokens, below the 20% second-entry share.
- rapporto leads with "report". "relationship" is as common.
- The -rsi gate uses at most 2-4 linked sentences per verb, so it is noisy.
  Reverted verbs show a combined gloss ("affidare: to entrust; affidarsi:
  to rely on").

## Engine (vocab-engine repo)
- Verb examples show conjugated forms: 462 verbs have at least one example
  without the infinitive. The engine could highlight the linked token.
- The "5. Sentences" label wraps at 390px width.

## Licence
- The build-time tagger model (spaCy it_core_news_sm) is CC BY-NC-SA 3.0.
  The pack ships no model files, and the project must stay non-commercial
  while this model is used. A commercial use would need a differently
  licensed tagger.

## Builder flags added for Spanish (off for Italian; each a v4 follow-up)
- `prefer_headword_sentence`: pick example sentences that show the headword
  (or an alt) first, then a 3sg present verb form. In Spanish it cut words
  whose examples never show the headword from 656 to 17 of 1998. Turning it
  on here changes sentence choice, so it needs a QA round.
- `derived_form_tags`: Wiktionary diminutive/augmentative form-of senses stop
  being inflections (es: señorita is not a form of señora). Italian has the
  same pattern (casetta, ragazzino); check links before enabling.
- `phrase_token_spans`, `homograph_by_translation`, `initial_noun_verb_homograph`,
  `translation_mismatch`: see engine/tools/packbuilder/README.md.

## Content policy (done 2026-09-24)
- `sensitive_re`, `drop_all_levels` and `sensitive_gloss_re` are on in it.py
  (engine b8cbdb6). A1/A2: 15 sentences replaced (1 of them removed at
  every level), 23 moved to B1; 0 glosses changed; word ids and passages.json unchanged.
  `lower_level_gloss_re` stays off (uccidere/morire/morto keep their level).

## Policy rebuild (2026-09-25, engine ff88f44)
- Word ceiling: 8 words moved to B1 (uccidere A1; arma, sangue, sesso,
  omicidio, droga, pistola, sessuale A2); band edges: musica A2->A1, metodo,
  consigliare, autorità, appunto, post, occhiata, tecnologia, zitto B1->A2.
  `lower_level_gloss_re` stays off; the shared `word_ceiling_re` covers it.
- Drop-everywhere (suicide/self-harm): s0553, s1849, s3131 removed; il suicidio
  (w1873) refilled from `tools/generated_examples.tsv`. Sentences 3,152 -> 3,141
  (36 removed, 25 added, by text). passages.json unchanged.
- anzi (w2185) and ovvero (w1794) still have no example sentence (none in the
  corpus passes the filters; pre-existing, not caused by the policy).

## Reading passages
- Regenerate `pack/*.js` and `index.html` after `pack/passages.json` changes.
  jsonify_pack.py appends PASSAGES to sentences.js, and until then
  validate_pack.py reports sentences.js as stale.
- "fine settimana" links only settimana, through the passages compound-head
  rule. The main sentence builder still has no compound table: see "fine
  settimana" under Sentence links above.
- The cosa noun/pronoun second-entry swap comes from the sentence builder, so
  passage links inherit it: cosa in "che cosa" can link the noun entry.
- A native-speaker pass over the 60 texts has not been done yet.
- Republished on engine 1dbca4d (one word id per token, phrase-part links):
  the 11 passage fallback chips are gone (0 tokens without a span).

## Republish 09e90bc (2026-09-29)
- Republish 09e90bc: sentence spans (18975/19012 linked words placed); inflected forms now cloze targets

## Republish ef44c6e (2026-09-30)
- Republish ef44c6e: no words moved (pack/*.json byte-identical); no override keys deleted; set-counter and no-voice planner fixes

## Republish 439df3d (2026-10-08, port wave 1)
- Republish 439df3d: typed modes, day-aware scheduling, reading rotation, goals, pairs, frequency tiers, Progress v2, redesigned tabs, session estimates. Pack diff vs f3e2a96: every word gains `ft` (100 ambient / 1385 core / 515 peripheral), pack.json gains the generic flag set + `eta`; nothing else (tools/eta.json from `eta_checks --calibrate --sessions 600`).
- Migration proof: rollback hash f3e2a96a20b6cc15249465368058e77e08760b80; previous live md5 index 928a5b5e92e72b351cafe82d14db6117, sw 58b15a5e7898da1cf29a1f9f3f146bed. Storage: new fields day/sn/t/u/f/p/pm/pv/pause/read.done s,ls/today.tw on first use; boot writes nothing; previous build ef44c6e/aa00571 carries them (migration [port] 9/9).
