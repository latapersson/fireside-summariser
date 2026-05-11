# Fireside Summariser

Turns a 50-minute founder fireside into two shareable audio segments — one for an AI-strategy audience, one for an operator audience.

Built for R[3]sidency, an AI, crypto and robotics accelerator that runs an intimate fireside series with founders. Each one is recorded, but almost none of them get distributed beyond the room they were held in.

## Demo

Two segments produced from a fireside between Richard Muirhead (Fabric) and Archie Hollingsworth (co-founder, Fyxer AI), recorded as part of the R[3]sidency programme.

**Reel A — Three contrarian bets** (3:00)  
Fyxer's bets on custom models, customer reach, and the AI talent ecosystem.

<audio controls src="output/reel_a.mp3"></audio>

**Reel B — Building for everyone but founders** (3:30)  
How Fyxer found product-market fit by deliberately ignoring tech-native customers.

<audio controls src="output/reel_b.mp3"></audio>

## How it works

```
input.mp4
   ↓ extract audio (ffmpeg)
input.wav
   ↓ transcribe (ElevenLabs Scribe v2)
transcript.json
   ↓ human picks line ranges in terminal
highlights.json
   ↓ extract clips (ffmpeg)
clips/*.wav
   ↓ Claude drafts narration → human edits
narration_final.txt
   ↓ synthesise narration (ElevenLabs TTS, custom voice)
narration/*.mp3
   ↓ stitch with 200ms gaps and loudness normalisation (ffmpeg)
output/reel_*.mp3
```

Transcription, voice design, and narration are ElevenLabs. Clip selection stays with the operator, the LLM stays in the pipeline where it has an advantage in turning transcripts into clean prose but the editorial calls are made by a human.

## Setup

```bash
git clone [repo]
cd fireside-summariser
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env  # add your ElevenLabs and Anthropic keys
```

Then point it at an MP4:

```bash
python summariser.py transcribe input.mp4
python summariser.py pick  # interactive
python summariser.py generate
```

Requires ffmpeg installed locally.

## What I'd build next

There are 14 firesides in this series. I've processed one. The obvious next step is doing the other 13: two segments each, roughly 28 pieces of distributable founder content sitting on a recording archive that almost nobody has heard.

But the more interesting question is what else falls out of the same transcript once it exists. Audio reels are the highest-effort downstream artefact but not the only one. The same fireside can produce a pull-quote thread for X, a long-form post for LinkedIn, a Q&A excerpt that lives on Substack, and a tagged transcript on the Fabric site doing SEO work. One source recording, several formats, near-zero marginal cost once the pipeline exists.

The audience-aware segmentation was also a surprise. Going in, I assumed I'd build one reel per fireside. Building the Archie reel taught me that the content can split into different themes and thus audiences: Fyxer's AI strategy speaks to one audience; Fyxer's customer-finding playbook speaks to another. The tool should probably suggest those splits before the operator picks clips, not after.

Where this gets interesting at the accelerator level is who ends up listening. The whole point of this content existing publicly is that the founders most likely to want into a programme like R[3]sidency are the same founders who'd press play on a fireside with Archie Hollingsworth. The right version of this tool tags every segment with a UTM, watches which segments drive R[3]sidency programme-page traffic, and feeds that signal back into which firesides get processed next and how they're framed. The content is the asset and the applicants are the product.

## Credits

- Source recording: Archie Hollingsworth and Richard Muirhead at Fabric's Zero-to-One Fireside Series, part of the R[3]sidency programme.
- Voice: custom voice generated via ElevenLabs Voice Design.
- All transcription and TTS: ElevenLabs (Scribe v2, Eleven v3).
