<img src="https://github.com/AssemblyAI/assemblyai-python-sdk/blob/master/assemblyai.png?raw=true" width="500"/>

---

[![CI Passing](https://github.com/AssemblyAI/assemblyai-python-sdk/actions/workflows/test.yml/badge.svg)](https://github.com/AssemblyAI/assemblyai-python-sdk/actions/workflows/test.yml)
[![GitHub License](https://img.shields.io/github/license/AssemblyAI/assemblyai-python-sdk)](https://github.com/AssemblyAI/assemblyai-python-sdk/blob/master/LICENSE)
[![PyPI version](https://badge.fury.io/py/assemblyai.svg)](https://badge.fury.io/py/assemblyai)
[![PyPI Python Versions](https://img.shields.io/pypi/pyversions/assemblyai)](https://pypi.python.org/pypi/assemblyai/)
![PyPI - Wheel](https://img.shields.io/pypi/wheel/assemblyai)
[![AssemblyAI Twitter](https://img.shields.io/twitter/follow/AssemblyAI?label=%40AssemblyAI&style=social)](https://twitter.com/AssemblyAI)
[![AssemblyAI YouTube](https://img.shields.io/youtube/channel/subscribers/UCtatfZMf-8EkIwASXM4ts0A)](https://www.youtube.com/@AssemblyAI)
[![Discord](https://img.shields.io/discord/875120158014853141?logo=discord&label=Discord&link=https%3A%2F%2Fdiscord.com%2Fchannels%2F875120158014853141&style=social)
](https://assemblyai.com/discord)

# AssemblyAI's Python SDK

> _Build with AI models that can transcribe and understand audio_

With a single API call, get access to AI models built on the latest AI breakthroughs to transcribe and understand audio and speech data securely at large scale.

# Overview

- [AssemblyAI's Python SDK](#assemblyais-python-sdk)
- [Overview](#overview)
- [Documentation](#documentation)
- [Quick Start](#quick-start)
  - [Installation](#installation)
  - [Examples](#examples)
    - [**Core Examples**](#core-examples)
    - [**LeMUR Examples**](#lemur-examples)
    - [**Audio Intelligence Examples**](#audio-intelligence-examples)
    - [**Real-Time Examples**](#real-time-examples)
  - [Playgrounds](#playgrounds)
- [Advanced](#advanced)
  - [How the SDK handles Default Configurations](#how-the-sdk-handles-default-configurations)
    - [Defining Defaults](#defining-defaults)
    - [Overriding Defaults](#overriding-defaults)
  - [Synchronous vs Asynchronous](#synchronous-vs-asynchronous)
  - [Polling Intervals](#polling-intervals)
  - [Retrieving Existing Transcripts](#retrieving-existing-transcripts)
    - [Retrieving a Single Transcript](#retrieving-a-single-transcript)
    - [Retrieving Multiple Transcripts as a Group](#retrieving-multiple-transcripts-as-a-group)
    - [Retrieving Transcripts Asynchronously](#retrieving-transcripts-asynchronously)

# Documentation

Visit our [AssemblyAI API Documentation](https://www.assemblyai.com/docs) to get an overview of our models!

# Quick Start

## Installation

```bash
pip install -U assemblyai
```

## Examples

Before starting, you need to set the API key. If you don't have one yet, [**sign up for one**](https://www.assemblyai.com/dashboard/signup)!

```python
import assemblyai as aai

# set the API key
aai.settings.api_key = f"{ASSEMBLYAI_API_KEY}"
```

---

### **Core Examples**

<details>
  <summary>Transcribe a Local Audio File</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("./my-local-audio-file.wav")

print(transcript.text)
```

</details>

<details>
  <summary>Transcribe an URL</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3")

print(transcript.text)
```

</details>

<details>
  <summary>Transcribe binary data</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()

# Binary data is supported directly:
transcript = transcriber.transcribe(data)

# Or: Upload data separately:
upload_url = transcriber.upload_file(data)
transcript = transcriber.transcribe(upload_url)
```

</details>

<details>
  <summary>Export Subtitles of an Audio File</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3")

# in SRT format
print(transcript.export_subtitles_srt())

# in VTT format
print(transcript.export_subtitles_vtt())
```

</details>

<details>
  <summary>List all Sentences and Paragraphs</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3")

sentences = transcript.get_sentences()
for sentence in sentences:
  print(sentence.text)

paragraphs = transcript.get_paragraphs()
for paragraph in paragraphs:
  print(paragraph.text)
```

</details>

<details>
  <summary>Search for Words in a Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3")

matches = transcript.word_search(["price", "product"])

for match in matches:
  print(f"Found '{match.text}' {match.count} times in the transcript")
```

</details>

<details>
  <summary>Add Custom Spellings on a Transcript</summary>

```python
import assemblyai as aai

config = aai.TranscriptionConfig()
config.set_custom_spelling(
  {
    "Kubernetes": ["k8s"],
    "SQL": ["Sequel"],
  }
)

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3", config)

print(transcript.text)
```

</details>

<details>
  <summary>Upload a file</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
upload_url = transcriber.upload_file(data)
```

</details>

<details>
  <summary>Delete a transcript</summary>

```python
import assemblyai as aai

transcript = aai.Transcriber().transcribe(audio_url)

aai.Transcript.delete_by_id(transcript.id)
```

</details>

<details>
  <summary>List transcripts</summary>

This returns a page of transcripts you created.

```python
import assemblyai as aai

transcriber = aai.Transcriber()

page = transcriber.list_transcripts()
print(page.page_details)  # Page details
print(page.transcripts)  # List of transcripts
```

You can apply filter parameters:

```python
params = aai.ListTranscriptParameters(
    limit=3,
    status=aai.TranscriptStatus.completed,
)
page = transcriber.list_transcripts(params)
```

You can also paginate over all pages by using the helper property `before_id_of_prev_url`.

The `prev_url` always points to a page with older transcripts. If you extract the `before_id`
of the `prev_url` query parameters, you can paginate over all pages from newest to oldest.

```python
transcriber = aai.Transcriber()

params = aai.ListTranscriptParameters()

page = transcriber.list_transcripts(params)
while page.page_details.before_id_of_prev_url is not None:
    params.before_id = page.page_details.before_id_of_prev_url
    page = transcriber.list_transcripts(params)
```

</details>

---

### **LeMUR Examples**

<details>
  <summary>Use LeMUR to Summarize Multiple Transcripts</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript_group = transcriber.transcribe_group(
    [
        "https://example.org/customer1.mp3",
        "https://example.org/customer2.mp3",
    ],
)

result = transcript_group.lemur.summarize(
  context="Customers asking for cars",
  answer_format="TLDR"
)

print(result.response)
```

</details>

<details>
  <summary>Use LeMUR to Ask Questions on a Single Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/customer.mp3")

# ask some questions
questions = [
    aai.LemurQuestion(question="What car was the customer interested in?"),
    aai.LemurQuestion(question="What price range is the customer looking for?"),
]

result = transcript.lemur.question(questions)

for q in result.response:
    print(f"Question: {q.question}")
    print(f"Answer: {q.answer}")
```

</details>

<details>
  <summary>Use LeMUR to Ask for Action Items from a Single Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/customer.mp3")

result = transcript.lemur.action_items(
    context="Customers asking for help with resolving their problem",
    answer_format="Three bullet points",
)

print(result.response)
```


</details>

<details>
  <summary>Use LeMUR to Ask Anything with a Custom Prompt</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/customer.mp3")

result = transcript.lemur.task(
  "You are a helpful coach. Provide an analysis of the transcript "
  "and offer areas to improve with exact quotes. Include no preamble. "
  "Start with an overall summary then get into the examples with feedback.",
)

print(result.response)
```

</details>


<details>
  <summary>Use LeMUR to with Input Text</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
config = aai.TranscriptionConfig(
  speaker_labels=True,
)
transcript = transcriber.transcribe("https://example.org/customer.mp3", config=config)

# Example converting speaker label utterances into LeMUR input text
text = ""

for utt in transcript.utterances:
    text += f"Speaker {utt.speaker}:\n{utt.text}\n"

result = aai.Lemur().task(
  "You are a helpful coach. Provide an analysis of the transcript "
  "and offer areas to improve with exact quotes. Include no preamble. "
  "Start with an overall summary then get into the examples with feedback.",
  input_text=text
)

print(result.response)
```

</details>

<details>
  <summary>Delete data previously sent to LeMUR</summary>

```python
import assemblyai as aai

# Create a transcript and a corresponding LeMUR request that may contain senstive information.
transcriber = aai.Transcriber()
transcript_group = transcriber.transcribe_group(
  [
    "https://example.org/customer1.mp3",
  ],
)

result = transcript_group.lemur.summarize(
  context="Customers providing sensitive, personally identifiable information",
  answer_format="TLDR"
)

# Get the request ID from the LeMUR response
request_id = result.request_id

# Now we can delete the data about this request
deletion_result = aai.Lemur.purge_request_data(request_id)
print(deletion_result)
```

</details>

---

### **Audio Intelligence Examples**

<details>
  <summary>PII Redact a Transcript</summary>

```python
import assemblyai as aai

config = aai.TranscriptionConfig()
config.set_redact_pii(
  # What should be redacted
  policies=[
      aai.PIIRedactionPolicy.credit_card_number,
      aai.PIIRedactionPolicy.email_address,
      aai.PIIRedactionPolicy.location,
      aai.PIIRedactionPolicy.person_name,
      aai.PIIRedactionPolicy.phone_number,
  ],
  # How it should be redacted
  substitution=aai.PIISubstitutionPolicy.hash,
)

transcriber = aai.Transcriber()
transcript = transcriber.transcribe("https://example.org/audio.mp3", config)
```

To request a copy of the original audio file with the redacted information "beeped" out, set `redact_pii_audio=True` in the config.
Once the `Transcript` object is returned, you can access the URL of the redacted audio file with `get_redacted_audio_url`, or save the redacted audio directly to disk with `save_redacted_audio`.

```python
import assemblyai as aai

transcript = aai.Transcriber().transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(
    redact_pii=True,
    redact_pii_policies=[aai.PIIRedactionPolicy.person_name],
    redact_pii_audio=True
  )
)

redacted_audio_url = transcript.get_redacted_audio_url()
transcript.save_redacted_audio("redacted_audio.mp3")
```

[Read more about PII redaction here.](https://www.assemblyai.com/docs/Models/pii_redaction)

</details>
<details>
  <summary>Summarize the content of a transcript over time</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(auto_chapters=True)
)

for chapter in transcript.chapters:
  print(f"Summary: {chapter.summary}")  # A one paragraph summary of the content spoken during this timeframe
  print(f"Start: {chapter.start}, End: {chapter.end}")  # Timestamps (in milliseconds) of the chapter
  print(f"Healine: {chapter.headline}")  # A single sentence summary of the content spoken during this timeframe
  print(f"Gist: {chapter.gist}")  # An ultra-short summary, just a few words, of the content spoken during this timeframe
```

[Read more about auto chapters here.](https://www.assemblyai.com/docs/Models/auto_chapters)

</details>

<details>
  <summary>Summarize the content of a transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(summarization=True)
)

print(transcript.summary)
```

By default, the summarization model will be `informative` and the summarization type will be `bullets`. [Read more about summarization models and types here](https://www.assemblyai.com/docs/Models/summarization#types-and-models).

To change the model and/or type, pass additional parameters to the `TranscriptionConfig`:

```python
config=aai.TranscriptionConfig(
  summarization=True,
  summary_model=aai.SummarizationModel.catchy,
  summary_type=aai.SummarizationType.headline
)
```

</details>
<details>
  <summary>Detect Sensitive Content in a Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(content_safety=True)
)


# Get the parts of the transcript which were flagged as sensitive
for result in transcript.content_safety.results:
  print(result.text)  # sensitive text snippet
  print(result.timestamp.start)
  print(result.timestamp.end)

  for label in result.labels:
    print(label.label)  # content safety category
    print(label.confidence) # model's confidence that the text is in this category
    print(label.severity) # severity of the text in relation to the category

# Get the confidence of the most common labels in relation to the entire audio file
for label, confidence in transcript.content_safety.summary.items():
  print(f"{confidence * 100}% confident that the audio contains {label}")

# Get the overall severity of the most common labels in relation to the entire audio file
for label, severity_confidence in transcript.content_safety.severity_score_summary.items():
  print(f"{severity_confidence.low * 100}% confident that the audio contains low-severity {label}")
  print(f"{severity_confidence.medium * 100}% confident that the audio contains mid-severity {label}")
  print(f"{severity_confidence.high * 100}% confident that the audio contains high-severity {label}")

```

[Read more about the content safety categories.](https://www.assemblyai.com/docs/Models/content_moderation#all-labels-supported-by-the-model)

By default, the content safety model will only include labels with a confidence greater than 0.5 (50%). To change this, pass `content_safety_confidence` (as an integer percentage between 25 and 100, inclusive) to the `TranscriptionConfig`:

```python
config=aai.TranscriptionConfig(
  content_safety=True,
  content_safety_confidence=80,  # only include labels with a confidence greater than 80%
)
```

</details>
<details>
  <summary>Analyze the Sentiment of Sentences in a Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(sentiment_analysis=True)
)

for sentiment_result in transcript.sentiment_analysis:
  print(sentiment_result.text)
  print(sentiment_result.sentiment)  # POSITIVE, NEUTRAL, or NEGATIVE
  print(sentiment_result.confidence)
  print(f"Timestamp: {sentiment_result.start} - {sentiment_result.end}")
```

If `speaker_labels` is also enabled, then each sentiment analysis result will also include a `speaker` field.

```python
# ...

config = aai.TranscriptionConfig(sentiment_analysis=True, speaker_labels=True)

# ...

for sentiment_result in transcript.sentiment_analysis:
  print(sentiment_result.speaker)
```

[Read more about sentiment analysis here.](https://www.assemblyai.com/docs/Models/sentiment_analysis)

</details>
<details>
  <summary>Identify Entities in a Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(entity_detection=True)
)

for entity in transcript.entities:
  print(entity.text) # i.e. "Dan Gilbert"
  print(entity.entity_type) # i.e. EntityType.person
  print(f"Timestamp: {entity.start} - {entity.end}")
```

[Read more about entity detection here.](https://www.assemblyai.com/docs/Models/entity_detection)

</details>
<details>
  <summary>Detect Topics in a Transcript (IAB Classification)</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(iab_categories=True)
)

# Get the parts of the transcript that were tagged with topics
for result in transcript.iab_categories.results:
  print(result.text)
  print(f"Timestamp: {result.timestamp.start} - {result.timestamp.end}")
  for label in result.labels:
    print(label.label)  # topic
    print(label.relevance)  # how relevant the label is for the portion of text

# Get a summary of all topics in the transcript
for label, relevance in transcript.iab_categories.summary.items():
  print(f"Audio is {relevance * 100}% relevant to {label}")
```

[Read more about IAB classification here.](https://www.assemblyai.com/docs/Models/iab_classification)

</details>
<details>
  <summary>Identify Important Words and Phrases in a Transcript</summary>

```python
import assemblyai as aai

transcriber = aai.Transcriber()
transcript = transcriber.transcribe(
  "https://example.org/audio.mp3",
  config=aai.TranscriptionConfig(auto_highlights=True)
)

for result in transcript.auto_highlights.results:
  print(result.text) # the important phrase
  print(result.rank) # relevancy of the phrase
  print(result.count) # number of instances of the phrase
  for timestamp in result.timestamps:
    print(f"Timestamp: {timestamp.start} - {timestamp.end}")

```

[Read more about auto highlights here.](https://www.assemblyai.com/docs/Models/key_phrases)

</details>

---

### **Real-Time Examples**

[Read more about our Real-Time service.](https://www.assemblyai.com/docs/Guides/real-time_streaming_transcription)

<details>
  <summary>Stream your Microphone in Real-Time</summary>

```python
import assemblyai as aai

def on_open(session_opened: aai.RealtimeSessionOpened):
  "This function is called when the connection has been established."

  print("Session ID:", session_opened.session_id)

def on_data(transcript: aai.RealtimeTranscript):
  "This function is called when a new transcript has been received."

  if not transcript.text:
    return

  if isinstance(transcript, aai.RealtimeFinalTranscript):
    print(transcript.text, end="\r\n")
  else:
    print(transcript.text, end="\r")

def on_error(error: aai.RealtimeError):
  "This function is called when an error occurs."

  print("An error occured:", error)

def on_close():
  "This function is called when the connection has been closed."

  print("Closing Session")


# Create the Real-Time transcriber
transcriber = aai.RealtimeTranscriber(
  on_data=on_data,
  on_error=on_error,
  sample_rate=44_100,
  on_open=on_open, # optional
  on_close=on_close, # optional
)

# Start the connection
transcriber.connect()

# Open a microphone stream
microphone_stream = aai.extras.MicrophoneStream()

# Press CTRL+C to abort
transcriber.stream(microphone_stream)

transcriber.close()
```

</details>

<details>
  <summary>Transcribe a Local Audio File in Real-Time</summary>

```python
import assemblyai as aai


def on_data(transcript: aai.RealtimeTranscript):
  "This function is called when a new transcript has been received."

  if not transcript.text:
    return

  if isinstance(transcript, aai.RealtimeFinalTranscript):
    print(transcript.text, end="\r\n")
  else:
    print(transcript.text, end="\r")

def on_error(error: aai.RealtimeError):
  "This function is called when the connection has been closed."

  print("An error occured:", error)


# Create the Real-Time transcriber
transcriber = aai.RealtimeTranscriber(
  on_data=on_data,
  on_error=on_error,
  sample_rate=44_100,
)

# Start the connection
transcriber.connect()

# Only WAV/PCM16 single channel supported for now
file_stream = aai.extras.stream_file(
  filepath="audio.wav",
  sample_rate=44_100,
)

transcriber.stream(file_stream)

transcriber.close()
```

</details>

<details>
  <summary>End-of-utterance controls</summary>

```python
transcriber = aai.RealtimeTranscriber(...)

# Manually end an utterance and immediately produce a final transcript.
transcriber.force_end_utterance()

# Configure the threshold for automatic utterance detection.
transcriber = aai.RealtimeTranscriber(
    ...,
    end_utterance_silence_threshold=500
)

# Can be changed any time during a session.
# The valid range is between 0 and 20000.
transcriber.configure_end_utterance_silence_threshold(300)
```

</details>

<details>
  <summary>Disable partial transcripts</summary>

```python
# Set disable_partial_transcripts to `True`
transcriber = aai.RealtimeTranscriber(
    ...,
    disable_partial_transcripts=True
)
```

</details>

<details>
  <summary>Enable extra session information</summary>

```python
# Define a callback to handle the extra session information message
def on_extra_session_information(data: aai.RealtimeSessionInformation):
    "This function is called when a session information message has been received."

    print(data.audio_duration_seconds)

# Configure the RealtimeTranscriber
transcriber = aai.RealtimeTranscriber(
    ...,
    on_extra_session_information=on_extra_session_information,
)
```

</details>

---

## Playgrounds

Visit one of our Playgrounds:

- [LeMUR Playground](https://www.assemblyai.com/playground/v2/source)
- [Transcription Playground](https://www.assemblyai.com/playground)

# Advanced

## How the SDK handles Default Configurations

### Defining Defaults

When no `TranscriptionConfig` is being passed to the `Transcriber` or its methods, it will use a default instance of a `TranscriptionConfig`.

If you would like to re-use the same `TranscriptionConfig` for all your transcriptions,
you can set it on the `Transcriber` directly:

```python
config = aai.TranscriptionConfig(punctuate=False, format_text=False)

transcriber = aai.Transcriber(config=config)

# will use the same config for all `.transcribe*(...)` operations
transcriber.transcribe("https://example.org/audio.wav")
```

### Overriding Defaults

You can override the default configuration later via the `.config` property of the `Transcriber`:

```python
transcriber = aai.Transcriber()

# override the `Transcriber`'s config with a new config
transcriber.config = aai.TranscriptionConfig(punctuate=False, format_text=False)
```

In case you want to override the `Transcriber`'s configuration for a specific operation with a different one, you can do so via the `config` parameter of a `.transcribe*(...)` method:

```python
config = aai.TranscriptionConfig(punctuate=False, format_text=False)
# set a default configuration
transcriber = aai.Transcriber(config=config)

transcriber.transcribe(
    "https://example.com/audio.mp3",
    # overrides the above configuration on the `Transcriber` with the following
    config=aai.TranscriptionConfig(dual_channel=True, disfluencies=True)
)
```

## Synchronous vs Asynchronous

Currently, the SDK provides two ways to transcribe audio files.

The synchronous approach halts the application's flow until the transcription has been completed.

The asynchronous approach allows the application to continue running while the transcription is being processed. The caller receives a [`concurrent.futures.Future`](https://docs.python.org/3/library/concurrent.futures.html) object which can be used to check the status of the transcription at a later time.

You can identify those two approaches by the `_async` suffix in the `Transcriber`'s method name (e.g. `transcribe` vs `transcribe_async`).

## Polling Intervals

By default we poll the `Transcript`'s status each `3s`. In case you would like to adjust that interval:

```python
import assemblyai as aai

aai.settings.polling_interval = 1.0
```

## Retrieving Existing Transcripts

### Retrieving a Single Transcript

If you previously created a transcript, you can use its ID to retrieve it later.

```python
import assemblyai as aai

transcript = aai.Transcript.get_by_id("<TRANSCRIPT_ID>")

print(transcript.id)
print(transcript.text)
```

### Retrieving Multiple Transcripts as a Group

You can also retrieve multiple existing transcripts and combine them into a single `TranscriptGroup` object. This allows you to perform operations on the transcript group as a single unit, such as querying the combined transcripts with LeMUR.

```python
import assemblyai as aai

transcript_group = aai.TranscriptGroup.get_by_ids(["<TRANSCRIPT_ID_1>", "<TRANSCRIPT_ID_2>"])

summary = transcript_group.lemur.summarize(context="Customers asking for cars", answer_format="TLDR")

print(summary)
```

### Retrieving Transcripts Asynchronously

Both `Transcript.get_by_id` and `TranscriptGroup.get_by_ids` have asynchronous counterparts, `Transcript.get_by_id_async` and `TranscriptGroup.get_by_ids_async`, respectively. These functions immediately return a `Future` object, rather than blocking until the transcript(s) are retrieved.

See the above section on [Synchronous vs Asynchronous](#synchronous-vs-asynchronous) for more information.
