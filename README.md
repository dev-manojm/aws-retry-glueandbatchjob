# aws-retry-glueandbatchjob
This code is used for re-ingesting the files that are failed during the batch job or glue job level
Whenever a batch job is failed for a file, That gets addressed with the help of an event rule.

The event pattern:


{
  "detail-type": [
    "Batch Job State Change"
  ],
  "source": [
    "aws.batch"
  ],
  "detail": {
    "status": [
      "FAILED"
    ],
    "jobQueue": [
      "arn:aws:batch:us-east-1:455077397705:job-queue/ingestor-s3-to-sqs-dev-us"
    ]
  }
}
This event will be used for 2 purposes:

This sends an email notification that the Batch job has failed with adequate details.


Triggers a lambda function to clean the file for re-ingestion.

The lambda function reads the file from the event, cleans it, renames it and puts it back in the same position where it was before.

The Batch job on creation of putObject event gets triggered again and the clean file is placed for processing via batch job.

If for some reason the re-ingested file is failed again. the lambda and SNS are still activated but this time lambda doesnt clean the file instead sends an alert(email or slack yet to be decided) that manual intervention is needed for the said file.
