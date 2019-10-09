# SiteReliabilityEngineering
I read the entire 'Site Reliability Engineering - Google' book so you didn't have to 🦸‍♂️

<<<<<<< HEAD
This is an ongoing list of notes on SRE. I pay carefully attention to metrics and the math behind them 👨‍🔬



Site Reliability Engineering - Google Style

Reliability Mathematics: https://www.reliabilityeducation.com/reliabilityeducation/ReliabilityPredictionBasics.pdf

SLO (Service Level Objective) - A quantitative measurement of time or quantity of actions that must take place to enter SLA (repercussions). Internal thresholds set to alert the SLA violation. Quantitatively stronger than SLA. Services can have multiple SLO’s.

Example: HTTP (SLO) 200ms. If a request takes longer than 200ms you will enter SLA (usually financial repercussions). An SRE engineer needs to be able to anticipate (ideally) or remedy (more common) a failed SLO.

SLA (Service Level Agreement) - Essentially the consequences of a failed SLO. Usually comes in the form of direct or indirect monetary compensation.

Example: GCP breaks their HTTP SLO. GCP reimburses the company with $100 in cloud credits.

The Happiness Test - The minimum threshold to ensure that customers are happy.


Measuring Reliability

Example: Netflix - Playback latency (HTTP). Packet loss in the middle of a video.

SLI (Service Level Indicators) - The metrics you define to quantitatively measure your system performance.

Example: Error Rate (Network Health) - (success / total requests) * 100
Example: Error Rate (Network Health) - (success / throughput) * 100

Measuring Reliability (Edge Case) - Not every organization and/or system is linear. There are cases when you will need exponentially better service to a customer versus your standard service you normally offer.

Example: Black Friday - It is expected that Company Y will have an N% increase in their website (read: client) and thus will require X% increase in the “triangle of success”.

Triangle of Success - RAS 

Reliability - % of time that the system functions properly for the user.
Availability - % of time that the system is up and running.
Scalability - # of users that the system can serve reliably.

Availability is non-linearly related to customer happiness.
Availability is inversely proportional to the ability to push out new features.
Reliability can be increased by decreasing the dev release cycle, increasing testing, and more manual analysis.

Never Want 100% - The marginal cost to make an already reliable system more reliable often times exceeds the value of delivering this to the customers.

Marginal cost in this case is how much it would cost (engineer time, compute cost, etc.) to make a proposed change.

Value to customers in this case could be thought of the probability that new customers use the service due to proposed change and/or the probability of risk that you will lose a customer.


How To Determine Reliability

(1) Measure your SLO achieved and be above the target.
	Q: What do the users need and how does the system currently perform?

(2) Measure how SLI is performing against the target.
	Q: Will increasing the service availability result in positive externalities or negative externalities to the business function?

Note: If you make your service more reliable than an individuals ISP, your customer is going to blame the ISP, not you.


Iteration Process 

Review a new SLO after 3 months. Follow up review after 6-12 months.


Error Budgets

Requires executive buy-in.
Balance reliability with feature velocity.

Error Budget = 1 - SLO
Allowed Downtime = SLO * 28 (days) * 24 (hours/day) * 60 (minutes/hour)

The single largest source of outages is change to a system. New features = lower service availability.

Note: non-linear correlation between the relationship of new features and lowered service.

Example: To improve reliability of a new feature incorporated into a system you could find that it will cost 10x the previous amount to ensure that the new system is reliable.

Advanced Topics:
“Dynamic release cadence” - Throttle back the grip on disallowing features to be released due to an error budget that was overly frugal.

“Rainy day fund” - Rollover error budget that covers unexpected events.

“Budget based alerts” - Send alert if recent errors are > X% of your monthly budget.

“Silver Bullets” - Error budget is already out. SRE doesn’t want to support the new feature. SWE says new feature is vital to company and has N-silver bullets. The SWE would have to have seniority and use one of their silver bullets in this case. DO NOT ROLLOVER.

Silver Bullets are treated as a failure and would require a post-mordem.


Trade-Off Theory

How to make devs happy - integrations testing, automated canary analysis (ACA), rollback.

How to reduce scale of failure amongst users - Route traffic to a small percentage of users with a new image and study how the system responds to the changes. This is also a great way to discover and eliminate SPOF (single point of failure).

TTD - time to detect an issue in a system.
TTR - time to resolve the issue in the system.
TTF - time elapsed between failures.

Error Impact (TBF) = (TTD + TTR) * impact (%) / TTF

Improve reliability - reduce numerator OR increase denominator

Improve TTD - implement systems to get alerts to the right person faster (reduce detection time).

Improve TTR - implement systems to fix outages faster. Examples: develop a playbook, increased data parsing and log analysis. Take a failed zone offline and redirect traffic to an available zone while the affected zone is getting repaired.

Improve Impact % - implement system to roll out new features to a very small set of users (note: Find users that fall within DAU and are not your “core” user base. Find users that you “can afford to lose” and test it on them.) Give changes time to bake.

Reducing TTF - Decrease the probability that a failure ever happens again. Example: re-routing traffic from a failed region over to a region that is healthy.


Reliability Operations Best Practices

Periodically report the worst customers, worst region, uneven error budget distribution. Focus extra hard on those regions.

Standardize infrastructure.

Consult on system design.

Rollback speed.

Phased rollouts.

Quantifying User Satisfaction

Think about the reliablility from the users point of view.

How to measure the happiness? We define a SLI and measure how it changes over time.

Want an SLI that has a linear relationship (predictable) with the happiness of the users.

Predictability is very important because you will be making engineering changes based on the data.

Relationship between latency and user happiness is an “S” curve (non-linear).

Example: Website is slow to load or respond to other embedded features. User leaves site. Count up the speed and the quantity of users that left the site in this window of time as a ratio of users that didn’t. You will have a quantified metric of how unhappy the event made users.

Properties of SLI

Standard (computer) operational metrics: Load average, CPU util, memory usage, bandwidth.

 CPU bound = slow service = unhappy user

SLI is a measurement of user experience (quantitative)

Services internal state metrics: thread pull fullness, request queue length, request queue outages

^ No linear relationship!

SLI = good events / valid events
0%-100%
Consistent format


SLI aggregated over a long time period is needed to make a decision on the validity of the metric. Want high signal, low noise.

Measuring SLIs (Order of User Proximity HI->LOW)

1. Processing server side request logs
    1. Request backfill SLI logs. Get retroactive data to build a model prior to conception.
    2. Convoluted logs (data stitching), processing jobs, etc. can be stitched together and exported as a refined “good event” counter.
    3. Note: ingestion and processing will add significant latency between event TO observation in SLI.
    4. With the aforementioned in mind this is a poor way to build an emergency metric.
    5. Events that don’t hit the application server can’t be observed in the logs at all.
    6. Hard to measure complex user journeys (stateless server).
    7. Easy to capture application level metrics even easier to add.
    8. Application level metrics are lower latency than logs processing.

2. Service requests.
    1. Extract metrics off of the client load balancer.
    2. Closest point to the user and most data will not go unmeasured.
    3. Little to no engineering effort required since most cloud providers have this feature built in.
    4. Load balancers are stateless which makes it impossible to track sessions.
    5. Load balancers have no hand in response data which can be just as important. They have to rely on metadata.

3. Application servers have a conflict of interest since they are the ones who create the response data.
    1. They could not know that the responses are bad.
    2. Remedy: using a synthetic client to gather data unbiased.
    3. Problem: sythetic client could be prone to edge case behavior that could throw off the experiment.

4. Client telemetry is data straight at the source.
    1. Most reliable thing to measure due to proximity.
    2. Can be used to track the reliability of the CDNs or payment processors.
    3. Con: Measurement latency. Drain users mobile battery life. Causes trust issues.
    4. Not good for emergency responses.

5. Measuring at the client.
    1. Capture events that are outside the scope of your application. (Hardware issues, thanks Apple).
    2. Can drop the signal to noise ration significantly.
    3. Would force you to have to drop your SLO.

Quantified User Journey

SLI: request/response will tell us availability, latency, and quality of service.

Data Processing: coverage, correctness, freshness, throughput.

Storage: measure the durability of the storage layer.
	- Not a great metric since most data will not be lost unless there is a complete catastrophe

Measuring Different Types of Validity


HTTP(S): Parameters include host name, requested path to set the scope to a set of tasks or response handlers.

Problem with HTTP Status Code(s): You could have a 2xx status code (success) and the request body could be null. This would fail for the user. Error visibility in the JSON body only you would have to do parsing to ensure that it was successful.

Data Processing: Selection of inputs to set the scope to some data subset.

Request/Response SLIs: the ratio of successful requests received.

Which of the requests that are served are valid for the SLI?
How can you tell if the request was served with degrading quality? The mechanism that the system uses to degrade quality should mark responses as such.

Example: Availability of a VM - proportion of minutes that it was booted and availably via SSH. Note: could also ping the IP. Also, write logic as code and export a boolean value to the SLO monitoring system. Also, could have an integer SLI value to represent the number of minutes it was available. 

Request/Response Latency: % of requests that are served faster than some threshold. How accurate is the correlation between latency and user experience? Probably pretty high. A system can be optimized for this if we find a great coorelation. Example - prefetching, caching. This would increase the SLO. Since “S” shaped relationship you want 75-90% of the requests to fall into this area.

Example: When it’s ok to only sample ~75% of the requests? 
	Is when you have a cache in front of the service -> bimodal distribution of response latencies (double peak with one local maximum and a global maximum).

Latency - the proportion of work-queue tasks that are faster than threshold X. Users care about the time it takes to complete tasks. SREs care about the latency of the asynchronous queue ACK.

Latency Reporting - only report long running applications on their their success/failure.

Example: Threshold (T) = 30 minutes, Reported (R) = 120 minutes => 90 minutes of “unknown” failing. You can only make decisions off of data that you measure!

Example: Service that fans-out incoming requests to 10 different backends (each backend has 99.99% avail target).
99% of surface area (responses) cannot have a missing backend response.  99.9% must be served with <= 1 missing.



=======
This is an ongoing list of notes on SRE. I pay carefully attention to metrics and the math behind them  👨🏼‍🏫 
>>>>>>> 41fd6a8dcd8ddc72a4d12f1d6f34305a022ed716
