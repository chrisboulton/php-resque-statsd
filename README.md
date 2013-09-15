php-resque-statsd: PHP Resque StatsD
==========================================

php-resque-statsd implements [StatsD](http://github.com/etsy/statsd) metric
tracking into php-resque.

For each job picked up by php-resque, numerous metrics will be submitted to
StatsD, including counters to track the number of jobs executed, and timers to
track how much time php-resque workers spend working.

php-resque-statsd also includes support for tracking metrics for jobs scheduled
with [php-resque-scheduler](http://github.com/chrisboulton/php-resque-scheduler).
The appropriate listeners to track scheduled jobs are automatically registered,
so no extra work is required on your behalf.

## Using php-resque-statsd

php-resque-statsd exists as a single class (`lib/ResqueStatsD.php`), which has
no additional dependencies beyond php-resque itself.

To start tracking your jobs with StatsD, all you need to do is include
`ResqueStatsD.php` in your project.

If you're starting php-resque with the resque.php script supplied with
php-resque, all that is
required is a modification to the bootstrap file you supply to php-resque via
the `APP_INCLUDE` environment variable:

	require_once '/path/to/ResqueStatsD.php';

### StatsD Connection Details

php-resque-scheduler will automatically check for the following environment
variables if they exist and use them when connecting to StatsD:

 * `STATSD_HOST` / `STATSD_PORT`
 * `GRAPHITE_HOST`

To ease integration with existing setups, if either `STATSD_HOST` or
`GRAPHITE_HOST` include a single colon and then one or more numbers, this will
be interpretted as a HOST:PORT combination and both the host and port will be
set accordingly.

If you don't use environment variables in your project, you can still tell
php-resque-statsd where StatsD is located:

	$host = '127.0.0.1';
	$port = 8579;

	require_once '/path/to/ResqueStatsD.php';
	ResqueStatsd::setHost($host, $port);


## Metrics

php-resque-statsd prefixes all metrics it generates with `resque`. You can
override this behavior if desired:

	require_once '/path/to/ResqueStatsd.php';
	ResqueStatsd::setPrefix('resque.production');

### Queue Based Metrics

The metrics below are tracked for each queue, instead of each unique job:

*   **stats.resque.queue.QUEUE_NAME.enqueued**
    Counter of the number of jobs enqueued in this queue

*   **stats.resque.queue.QUEUE_NAME.finished**
	 Counter of the number of jobs successfully processed in this queue
	
*   **stats.resque.queue.QUEUE_NAME.failed**
	 Counter of the number of jobs that failed in this queue

*   **stats.timers.queue.QUEUE_NAME.processed**
	 Timer for jobs processed in this queue

*   **stats.resque.queue.QUEUE_NAME.scheduled**
	 If using php-resque-scheduler, number of jobs scheduled for future execution
	 in this queue

For example, for all jobs executed in the queue `transcode`, the following StatsD
metrics will be created:

* stats.resque.queue.transcode.enqueued
* stats.resque.queue.transcode.finished
* stats.resque.queue.transcode.failed
* stats.timers.resque.queue.transcode.processed
* stats.resque.queue.transcode.scheduled

### Job Based Metrics

Metrics are also tracked on a job level:

*   **stats.resque.job.JOB_CLASS.enqueued**
    Counter for the number of times this job has been enqueued

*   **stats.resque.job.JOB_CLASS.finished**
    Counter for the number of times this job has been successfully processed

*   **stats.resque.job.JOB_CLASS.failed**
    Counter for the number of times this job has failed

*   **stats.timers.job.JOB_CLASS.processed**
    Timer for the amount of time spent processing this job

*   **stats.resque.job.JOB_CLASS.scheduled**
    If using php-resque-scheduler, number of of times this job has been scheduled
    for future execution

For example, a job named `Job_SendEmail` the following metrics will be created:

* stats.resque.job.Job_SendEmail.enqueued
* stats.resque.job.Job_SendEmail.finished
* stats.resque.job.Job_SendEmail.failed
* stats.timers.resque.job.Job_SendEmail.processed
* stats.resque.job.Job_SendEmail.scheduled

## Contributors ##

* chrisboulton
