# ExAws.Transcribe

[AWS Transcribe](https://docs.aws.amazon.com/transcribe/latest/dg/API_Operations.html) service module for [ex_aws](https://github.com/ex-aws/ex_aws).


## Installation

The package can be installed by adding `ex_aws_transcribe` to your list of dependencies
in `mix.exs` along with a specific fork of `ex_aws` and your preferred JSON codec / http client:

```elixir
def deps do
  [
    {:ex_aws, git: "https://github.com/brianlow/ex_aws.git", branch: "transcribe", override: true},
    {:ex_aws_transcribe, "~> 0.2.1"},
    {:poison, "~> 3.0"},
    {:hackney, "~> 1.9"},
  ]
end
```

Notice this uses a specific fork of `ex_aws` which adds the AWS Transcribe endpoints. If this works for
you please vote / comment on [PR #611](https://github.com/ex-aws/ex_aws/pull/611) so this can use
a published version of ex_aws.


## Alternative Installation

If you'd prefer to use a published version of ex_aws you'll need to use `ExAws.perform/2`
with a config patched together from S3 (S3 and Transcribe have simlar ExAws.Configs).

```elixir
def deps do
  [
    {:ex_aws, "~> 2.1.0", override: true},
    ...
  ]
end

config = ExAws.Config.new(:s3, host: "transcribe.us-east-1.amazonaws.com", region: "us-east-1")

Transcribe.list_transaction_jobs() |> ExAws.Operation.perform(config)
```


## Basic Usage

```elixir
alias ExAws.Transcribe

# Submit a transcription job
Transcribe.start_transcription_job("MyJob", "s3://bucket/file.mp3", "mp3", "en-US") |> ExAws.request!

# List jobs
Transcribe.list_transaction_jobs() |> ExAws.request!

# Get a job and retrieve the transcript if complete
job = Transcribe.get_transaction_job("MyJob") |> ExAws.request!
transcript_url = get_in(job, ["TranscriptionJob", "Transcript", "TranscriptFileUri"])
{:ok, {_, _, body}} = :httpc.request(:get, {transcript_url, []}, [], [])
```

Full documentation can be found at <https://hexdocs.pm/ex_aws_transcribe>


## AWS Transcribe API Reference

<https://docs.aws.amazon.com/transcribe/latest/dg/API_Operations.html>

All options are handled as snake_case_atoms instead of CamelCase binaries as specified
in the Transcribe API. For example, `:media_format` would be `MediaFormat`.


