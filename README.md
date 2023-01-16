# github-backup-to-s3

A tool to backup all your github repos to a aws s3 bucket.
It can optionally backup just an organisation's repositories.

## Usage

copy `.env.example` and rename it `.env` and setup the config variables.

### AWS Credentials

create a new IAM user with *programmatic access* and add access to `putObject` on the specific bucket you want to backup.

*(no other permissions is needed)*

the policy could look something like:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "123456789",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::REPLACE-WITH-BUCKET-NAME/*"
            ]
        }
    ]
}
```

### Run in organisation module

In your `.env`, set:

`GITHUB_ORGANISATION` to the name of the Organisation

`BACKUP_MODE=organisation`


### Run locally

```
node index.js
```

### Deploy to aws lambda

it might be more useful to deploy it with a schedule on aws lambda, so it will backup with a given interval.

install [Serverless Framework](https://github.com/serverless/serverless)
```
npm install -g serverless
```

optionally make modifications to **serverless.yml** and run

choose the scheduling interval:
http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html

```
serverless deploy -v --schedule-expression 'rate(2 hours)'

// every hour
serverless deploy -v --schedule-expression 'rate(0 * * * ? *)'

// every 5 minute
serverless deploy -v --schedule-expression 'cron(*/5 * * * ? *)'
```
