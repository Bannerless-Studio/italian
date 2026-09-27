# Italian A1-B1 vocab pack

Static data pack for a language-agnostic vocab trainer (`key: "it"`). 2000
words spanning A1-B1, each with a short English gloss, plus example
sentences with translations and (where licence permits) native audio. The
Read tab adds 60 short reading passages with comprehension questions (see
"Reading passages" below).

**Live:** https://bannerless-studio.github.io/italian/

Open the link, pick a level (or take the placement test), and start a Today
session: short rounds of flashcard-style review mixed with new words, plus a
Read tab with short passages and comprehension questions, and typing practice
for spelling. Progress (what you've seen, what's due for review) is saved in
your browser only, and can be exported/imported as a file to move between
devices. The site works offline once loaded (it registers a service worker).

**Scope note:** this app gives the vocabulary base for B1 - words,
glosses, and example sentences with audio. The full B1 exam also needs
grammar, writing, and speaking practice, which this app does not teach.

**Data quality:** after three QA rounds, the final hand-checked samples are
as follows. A 60-word stratified sample (seed 303) has 59/60 correct
primary senses. A 60-sentence sample (seed 404) has 3 wrong word links out
of 363, and 58/60 sentences are fully correct. The round-2 pack scored
59/60 and 57/60 on seed 7. Known residuals are listed in `TODO.md`, and the
per-round rules and counts are in `tools/REPORT.md`.

**Content policy:** sentences on sexual content, vulgarity, suicide, threats,
violence, dying or death wishes, weapons, blood, poison, corpses, drugs or
abuse are kept out of A1/A2. A word that is itself on that list, such as
morire (to die) or l'arma (weapon), only ships with B1-level examples, and a
handful of words (uccidere, arma, sangue, sesso, omicidio, droga, pistola,
sessuale) ship at B1 only because their English gloss names killing, weapons,
blood, sex or drugs. Sentences about rape, sexual assault/abuse, suicide or
self-harm are removed at every level. See `TODO.md` for the exact rule history
and counts.

## What's in this repo

This repo holds the Italian data pack (`pack/`) and the data files its build
reads (`tools/`), plus [`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine)
as a git submodule at `engine/`, which holds the shared UI, drill logic and
pack builder used by every language in this trainer. See `tools/README.md`
for a file-by-file breakdown of `tools/`, and `CLAUDE.md` for the full
architecture and build commands.

## Rebuild and publish (maintainers)

```
git clone --recurse-submodules <this repo>   # or: git submodule update --init
cd italian && python3 -m venv .venv && source .venv/bin/activate
pip install -r tools/requirements.txt
python3 tools/build_pack.py && python3 engine/tools/jsonify_pack.py pack
./build.sh && ./check.sh
```

See `tools/README.md` for what each rebuild step reads/writes and `CLAUDE.md`
for the pinned commands, submodule-update flow and forbidden patterns.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`it_full.txt`, 2018 OpenSubtitles) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, gender | [kaikki.org](https://kaikki.org) Italian Wiktionary extract | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, noun gender, inflection map |
| POS tagging / lemmatisation (build time only) | [spaCy](https://spacy.io) (MIT) with the `it_core_news_sm` 3.8.0 model | model: CC BY-NC-SA 3.0 (trained on UD Italian ISDT) | corpus POS, lemma and sense choice; sentence word links. The pack ships no model files; this project is non-commercial. |
| Example sentences | [Tatoeba](https://tatoeba.org) `ita_sentences_detailed.tsv` | CC-BY 2.0 FR | sentence text (see `pack/attribution.json` for contributor usernames) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `links.tar.bz2` | CC-BY 2.0 FR / CC0 | English translations |
| Sentence audio | Tatoeba `sentences_with_audio.tar.bz2` | CC BY / CC BY-SA / CC0 (per clip, only permissive clips linked) | `sentences.json[].audio`; recorders per licence in `pack/attribution.json` |
| CEFR cross-check (not shipped) | [kotoshu/frequency-list-kelly](https://github.com/kotoshu/frequency-list-kelly) `it.json` | licence unclear, research use only | sanity-check only, read from `.cache/`, never copied into `pack/` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

Note: the Tatoeba CC0-only Italian subset (`ita_sentences_CC0.tsv.bz2`)
contains only ~19 sentences and was unusable alone, exactly as the brief
anticipated; the CC-BY 2.0 FR detailed export is used instead, with
attribution recorded in `pack/attribution.json`.

## Level bands

Candidate (lemma, POS) pairs are ranked by a blended frequency score, the
mean of log subtitle-rank and log `wordfreq`-rank. Levels:

- **A1** (600 words): every forced item (days, months, seasons, numbers 0-20 plus the
  tens, cento and mille, basic colours, sì/no/ciao/grazie/prego/scusa/per
  favore, a/e/o/è, and an A1 core list of everyday words in
  `tools/forced_a1.txt`), then the highest-ranked remaining words.
- **A2**: the next 700 by rank.
- **B1**: the next 700 by rank.

This is a simple, reproducible proxy for CEFR level — it is not an
official CEFR classification. `tools/REPORT.md` includes a cross-check
against the Kelly CEFR-tagged list where available.

## Reading passages (Read tab)

60 short reading texts, 20 each at A1, A2 and B1, with 4-5 comprehension
questions each. The texts were written for this pack, checked by an
automated QA pass rather than a native speaker. Tapping any word in a
passage shows its gloss, including inflected forms. A passage's spaced
re-read on Today (after 7 days) becomes a listening pass when audio is
available: the text stays hidden behind numbered play rows, and about half
the questions are audio-only.

Typing practice stays accent-lenient at A1/A2 (`perche` = `perché`), but a
fold-only match is rejected when it would spell another pack word (`la`
won't match `là`, and vice versa).
