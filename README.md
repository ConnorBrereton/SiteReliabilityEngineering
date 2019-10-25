# SiteReliabilityEngineering

This is an ongoing list of notes on SRE I've learned over the past couple of years.

I pay careful attention to metrics and the math behind them 👨‍🔬

<h1>Site Reliability Engineering - Google Style</h1>

<h3>Helpful Links</h4>

**[Reliability Mathematics](https://www.reliabilityeducation.com/reliabilityeducation/ReliabilityPredictionBasics.pdf)**

**[The 'S' Curve (Preface)](https://www.insanegrowth.com/podcast-google-s-curve-theory/)**

**[The 'S' Curve (Math)](https://en.wikipedia.org/wiki/Sigmoid_function)**

**[How Complex Systems Fail (Chicago)](http://web.mit.edu/2.75/resources/random/How%20Complex%20Systems%20Fail.pdf)**

**[Google SRE Coding Questions](https://careercup.com/page?pid=google-interview-questions&job=site-reliability-engineer-interview-questions)**

**[Path to SRE (manager)](https://danrl.com/blog/2019/path-to-srm/)**

**[MIT 6.033 Systems Engineering](http://web.mit.edu/6.033/www/)**


<h3>SLOs</h3>

**SLO (Service Level Objective)** - A quantitative measurement of time or quantity of actions that must take place to enter SLA (repercussions). Internal thresholds set to alert the SLA violation. Quantitatively stronger than SLA. Services can have multiple SLO’s.


*Example*: HTTP (SLO) 200ms. If a request takes longer than 200ms you will enter SLA (usually financial repercussions). An SRE engineer needs to be able to anticipate (ideally) or remedy (more common) a failed SLO.




<h3>SLAs</h3>

**SLA (Service Level Agreement)** - Essentially the consequences of a failed SLO. Usually comes in the form of direct or indirect monetary compensation.


*Example*: GCP breaks their HTTP SLO. GCP reimburses the company with $100 in cloud credits.

**The Happiness Test** - The minimum threshold to ensure that customers are happy.




<h3>Measuring Reliability</h3>

*Example*: Netflix - Playback latency (HTTP). Packet loss in the middle of a video.

**SLI (Service Level Indicators)** - The metrics you define to quantitatively measure your system performance.

*Example*: `Error Rate (Network Health) - (success / total requests) * 100`

*Example*: `Error Rate (Network Health) - (success / throughput) * 100`

**Measuring Reliability (Edge Case)** - Not every organization and/or system is linear. There are cases when you will need exponentially better service to a customer versus your standard service you normally offer.

*Example*: Black Friday - It is expected that Company Y will have an N% increase in their website (read: client) and thus will require X% increase in the “triangle of success”.




<h3>Triangle of Success - R.A.S.</h3>

**Reliability** - % of time that the system functions properly for the user.
**Availability** - % of time that the system is up and running.
**Scalability** - # of users that the system can serve reliably.

- Availability is non-linearly related to customer happiness.
- Availability is inversely proportional to the ability to push out new features.
- Reliability can be increased by decreasing the dev release cycle, increasing testing, and more manual analysis.

**Never Want 100%** - The marginal cost to make an already reliable system more reliable often times exceeds the value of delivering this to the customers.

Marginal cost in this case is how much it would cost (engineer time, compute cost, etc.) to make a proposed change.

Value to customers in this case could be thought of the probability that new customers use the service due to proposed change and/or the probability of risk that you will lose a customer.




<h3>How To Determine Reliability</h3>

1. Measure your SLO achieved and be above the target.

	❓: What do the users need and how does the system currently perform?

2. Measure how SLI is performing against the target.

	❓: Will increasing the service availability result in positive externalities or negative externalities to the business function?

*Note*: If you make your service more reliable than an individuals ISP, your customer is going to blame the ISP, not you.




<h3>Iteration Process</h3>

- Review a new SLO after 3 months. Follow up review after 6-12 months.




<h3>Error Budgets</h3>

- Requires executive buy-in.
- Balance reliability with feature velocity.

`Error Budget = 1 - SLO`

`Allowed Downtime = SLO * 28 (days) * 24 (hours/day) * 60 (minutes/hour)`

⚠️ The single largest source of outages is change to a system. New features = lower service availability.

*Note*: non-linear correlation between the relationship of new features and lowered service.

*Example*: To improve reliability of a new feature incorporated into a system you could find that it will cost 10x the previous amount to ensure that the new system is reliable.

**Advanced Error Budget Topics**:

*“Dynamic release cadence”* 
- Throttle back the grip on disallowing features to be released due to an error budget that was overly frugal.

*“Rainy day fund”* 
- Rollover error budget that covers unexpected events.

*“Budget based alerts”* 
- Send alert if recent errors are > X% of your monthly budget.

*“Silver Bullets”* 
- Error budget is already out. SRE doesn’t want to support the new feature. SWE says new feature is vital to company and has N-silver bullets. The SWE would have to have seniority and use one of their silver bullets in this case. DO NOT ROLLOVER.

⚠️ ️️️Silver Bullets are treated as a failure and would require a post-mordem. ⚠️




<h3>Trade-Off Theory</h3>

**How to make devs happy?** 
- Integration testing, automated canary analysis (ACA), rollback.

**How to reduce scale of failure amongst users?**
 - Route traffic to a small percentage of users with a new image and study how the system responds to the changes. This is also a great way to discover and eliminate SPOF (single point of failure).

*TTD* - Time to detect an issue in a system.

*TTR* - Time to resolve the issue in the system.

*TTF* - Time elapsed between failures.

`Error Impact (TBF) = (TTD + TTR) * impact (%) / TTF`

**How to improve reliability?**
- Reduce numerator OR increase denominator

**How to improve TTD?**
- Implement systems to get alerts to the right person faster (reduce detection time).

**How to improve TTR?** 
- Implement systems to fix outages faster. 


*Examples*: develop a playbook, increased data parsing and log analysis. Take a failed zone offline and redirect traffic to an available zone while the affected zone is getting repaired.

**How to improve impact % ?** 
- Implement system to roll out new features to a very small set of users (note: Find users that fall within *DAU* and are not your “core” user base. Find users that you “can afford to lose” and test it on them.) Give changes time to bake.

**How to reduce TTF?** 
- Decrease the probability that a failure ever happens again. 

*Example*: re-routing traffic from a failed region over to a region that is healthy.




<h3>Reliability Operations Best Practices</h3>

- Periodically report the worst customers, worst region, uneven error budget distribution. Focus extra hard on those regions.

- Standardize infrastructure.

- Consult SWE on system design.

- Rollback speed.

- Phased rollouts.




<h3>Quantifying User Satisfaction</h3>

- Think about the reliablility from the *users* point of view.

**How to measure the happiness?** 

- We define a SLI and measure how it changes over time.

- We want an SLI that has a linear relationship (predictable) with the happiness of the users.

- Predictability is very important because you will be making engineering changes based on the data.

- Relationship between latency and user happiness is an “S” curve (non-linear).

*Example*: Website is slow to load or respond to other embedded features. User leaves site. Count up the speed and the quantity of users that left the site in this window of time as a ratio of users that didn’t. You will have a quantified metric of how unhappy the event made users.




<h3>Properties of SLI</h3>

**Standard (computer) operational metrics**: Load average, CPU util, memory usage, bandwidth.

`CPU bound = slow service = unhappy user`

`SLI = good events / valid events`

SLI is a measurement of user experience (quantitative)

*Services internal state metrics*: thread pull fullness, request queue length, request queue outages

*SLI Range*: 0%-100%

*Benefits*: Consistent format

SLI aggregated over a long time period is needed to make a decision on the validity of the metric. Want high signal, low noise.




<h3>Measuring SLIs (Order of User Proximity HI->LOW)</h3>

1. **Processing server side request logs**
    1. Request backfill SLI logs. Get retroactive data to build a model prior to conception.
    2. Convoluted logs (data stitching), processing jobs, etc. can be stitched together and exported as a refined “good event” counter.
    3. Note: ingestion and processing will add significant latency between event TO observation in SLI.
    4. With the aforementioned in mind this is a poor way to build an emergency metric.
    5. Events that don’t hit the application server can’t be observed in the logs at all.
    6. Hard to measure complex user journeys (stateless server).
    7. Easy to capture application level metrics even easier to add.
    8. Application level metrics are lower latency than logs processing.

2. **Service requests.**
    1. Extract metrics off of the client load balancer.
    2. Closest point to the user and most data will not go unmeasured.
    3. Little to no engineering effort required since most cloud providers have this feature built in.
    4. Load balancers are stateless which makes it impossible to track sessions.
    5. Load balancers have no hand in response data which can be just as important. They have to rely on metadata.

3. **Application servers have a conflict of interest since they are the ones who create the response data.**
    1. They could not know that the responses are bad.
    2. Remedy: using a synthetic client to gather data unbiased.
    3. Problem: sythetic client could be prone to edge case behavior that could throw off the experiment.

4. **Client telemetry is data straight at the source.**
    1. Most reliable thing to measure due to proximity.
    2. Can be used to track the reliability of the CDNs or payment processors.
    3. Con: Measurement latency. Drain users mobile battery life. Causes trust issues.
    4. Not good for emergency responses.

5. **Measuring at the client.**
    1. Capture events that are outside the scope of your application. (Hardware issues, thanks Apple).
    2. Can drop the signal to noise ration significantly.
    3. Would force you to have to drop your SLO.




<h3>Quantified User Journey</h3>

*SLI*: Request/response will tell us availability, latency, and quality of service.

*Data Processing*: Coverage, correctness, freshness, throughput.

*Storage*: Measure the durability of the storage layer.
- Not a great metric since most data will not be lost unless there is a complete catastrophe




<h3>Measuring Different Types of Validity</h3>

*HTTP(S)*: Parameters include host name, requested path to set the scope to a set of tasks or response handlers.

*Problem with HTTP Status Code(s)*: 
- You could have a 2xx status code (success) and the request body could be null. This would fail for the user. 
- Error visibility in the JSON body only you would have to do parsing to ensure that it was successful.

*Data Processing*: Selection of inputs to set the scope to some data subset.

*Request/Response SLIs*: the ratio of successful requests received.

*Request/Response Latency*: % of requests that are served faster than some threshold.

**Which of the requests that are served are valid for the SLI?**

**How can you tell if the request was served with degrading quality?**
- The mechanism that the system uses to degrade quality should mark responses as such.


*Example*: Availability of a VM - proportion of minutes that it was booted and availably via SSH. 

*Note*:
- Could also ping the IP. Also, write logic as code and export a boolean value to the SLO monitoring system. 
- Also, could have an integer SLI value to represent the number of minutes it was available.

**How accurate is the correlation between latency and user experience?**

Probably pretty high. A system can be optimized for this if we find a great coorelation. Example - prefetching, caching. This would increase the SLO. Since “S” shaped relationship you want 75-90% of the requests to fall into this area.

*Example*: When it’s ok to only sample ~75% of the requests?

- When you have a cache in front of the service -> bimodal distribution of response latencies (double peak with one local maximum and a global maximum).

*Latency* 
- The proportion of work-queue tasks that are faster than threshold X. 
- Users care about the time it takes to complete tasks. 
- SREs care about the latency of the asynchronous queue ACK.

*Latency Reporting* - only report long running applications on their their success/failure.

*Example*:

`Threshold (T) = 30 minutes`

`Reported (R) = 120 minutes`

⚠️ 90 minutes of “unknown” failing. You can only make decisions off of data that you measure! ⚠️

*Example*:
- Service that fans-out incoming requests to 10 different backends (each backend has 99.99% avail. target).
- 99% of surface area (responses) cannot have a missing backend response.  99.9% must be served with <= 1 missing.




<h3>Data Processing SLIs</h3>

**Main topics**: Freshness, correctness, coverage, and throughput.

**Freshness**:
- Output freshness decay as a function of time and user input data. Utility is what mainly diminishes.

- Users expect that those outputs are up to date and are not aware of the degradation over time. Data pipelines have to be constantly rebuilt and checked to ensure they meet the freshness threshold.

**Freshness SLI**: Ratio of valid data updated frequency beyond threshold X.

- Time is measured as the duration between data input to batch process (t=0) and then process outputs to some other mechanism `(t=N)`. Freshness the time since this completion.

**Streaming (continuous processing)**: 
- Watermarked timestamp that tells the freshness as a function of time. (Time Series Data)

*Example*: 1/5 streaming shards slow (latency is not within threshold) therefore 20% of data is stale.

*Example*: Requests read data unevenly. The ratio of unread/total requests = N% that are stale.

**Correctness**: Users will independently verify that your data is correct. Need an SLI for measuring this.

*Correctness SLI*: Ratio of valid data producing the correct output.

*Example*: 
- Need to have an independent testing suite to verify the system is behaving correctly.
- Misbehaving system will cause data to be corrupted. 
- Do not test natively in the application that is processing the data as this is heavily biased. This is called an external source of truth.

*Coverage SLI*: The ratio of valid data that has been successfully processed.

*Example*: 
`Input records = 2,147,483,647`

`Records that thrower status symbol “OK” = 2,147,452,310`

`Coverage = IR/OK = 99.9985%`

_**Throughput SLI**_:
- Ratio of time when data processing rate (see freshness time to complete notes) is faster (less than) some threshold X.

`Throughput = events / time`

*Throughput thresholds (GB/s)*: 
- Best Effort (slow)
- Guaranteed (expected)
- Expedited (exceeded)

⚠️ Huge drops in throughput are almost guaranteed to cause angry customers. ⚠️




<h3>SLI Recommendations (Google’s Rec)</h3>

⚠️ Only need 1-3 SLIs for each part of the user journey. ⚠️

**Why?**
- Not all metrics are good SLIs
- Don’t overload the SREs
- Don’t want to create conflicting signals. Harder to solve problems.

*Example*:
- *SLI*: Something broke.
- *Metrics*: **This** broke.




<h3>Aggregating SLIs</h3>

*Example*: App Store. You have 4 main user “journeys”.
1. Visiting the homepage.
2. Search the store (database).
3. Search by category (indexed search).
4. Open specific page related to an app.

These user actions are nothing more than web requests so we have latency and availability SLI for them.

*Problem*: Variance in request rates can cause a SLI to get “lost” in the noise.

*Solution*: Assigning a weight to each component SLI based on traffic/importance reduces risk.


| Journey   | Good  | Fast  | Threshold |
|-----------|-------|-------|-----------|
| home      | 9994  | 9866  | 10000     |
| search    | 9989  | 9729  | 10000     |
| category  | 9997  | 9913  | 10000     |
| open page | 10000 | 9849  | 10000     |
| sum (ms)  | 39980 | 39356 | 40000     |
| browse    | 99.95 | 98.39 |           |




<h3>Bucketing</h3>

*Problem*:
- Varying thresholds for relevant SLOs (latency, freshness, throughput) increases complexity significantly.

*Solution*:
- Use “bucketed” thresholds to reduce complexity.

*Assumptions*:
- You can identify the requests correctly. Don’t have a CPU bottleneck (bucketing will bog down the speed).

*Note*:
- In distributed systems consistent writes have different latency than reads.

- Google has found that users are ok with slower write speeds (such as hitting the “submit” button). BUT, users expect FAST read speeds.

*Example thresholds*:
- READ (400ms (good), 1s (awful))
- BACKGROUND (5s)
- WRITE (1500ms)

*Slow target*: 50-75% requests faster than annoying.
*Long tail*: 1s awful 90% beat this.

*Third-party*: Users are generally understanding that third party (payment processor, identify auth) services will be slower. Ok to set reasonable thresholds.




<h3>Creating Realistic SLO Targets</h3>

*Google’s SLI Philosophy*: 
- User expectations are strongly tied to past performance. If a service was 10/10 last quarter and this quarter it is 7/10 the user will really experience that service as something like a 5-6/10.

*Problem*:
- New companies don’t have tons of data to work off of. Hard to set SLOs when there is no data to use.
Solution: Do nothing for now and just work on gathering data.

*Notes*:
- Never assume users are OK with status quo. Use data to drive decision making.




<h3>Continuous Improvement</h3>

*Aspirational Targets*:
- These are what the business needs.

*Achievable Targets*:
- Achievable based on past performance.
- SLOs are dynamic in nature.



<h3>Developing SLOs and SLIs</h3>

1. Figure out SLI types & architect high level spec.

2. Describe in great detail the events being measured. Where/How the SLI will be measured?

3. Walk through user journey (trace through architecture) and identify coverage gaps. Document points of failure extensively. High risk failure points indicate a re-work needed.

4. Set SLO targets. Set measurement windows to gather performance data.



<h5>Example (Video Game System)</h5>

- You have a video game that has 50MM 30-day-trial *DAU* playing.

- Average 1-10MM users online at any give time.

- 1 new world each month added that causes traffic and revenue spikes.

- Largest revenue stream = real world ($$) -> game currency ($)

- 2nd largest revenue stream = PvP battles, mini-games, resource production.

- Largest player expense = settlement upgrades, defensive weapons for battles, setting up recruitment for other players to join them, etc.

- Mobile client & web UI applications.

**Mobile Client**

- *HTTP Requests*: JSON-RPC messages over REST HTTP.

- *Socket*: Open web-socket to receive game updates.

**Web**

- Browser talks to **web-server** over HTTPS

**Other**

- Leader boards are updated every 5 minutes.



<h3>Example (Profile Page)</h3>

- Use a sequence diagram to plan out the client <-> server interactions done by the infrastructure.

- *Client*: Requests the profile URL over HTTPS.


- *Web Server*:
    - Send HTML in response. 
    
    - Service the user's profile data from the user profile data store.

    - Render the leaderboard for the players location using latitude/longitude data.

    - Build the HTML response for player and send back to client.

- *CDN*: Content delivery network will automatically serve the CSS/JavaScript via HTML response.

- *Load Balancer*: Accepts the incoming request and forwards it to a pool of web servers.



<h3>Refining SLI Spec.</h3>

*Steps*:

- How do you want to measure the performance of the service against users expectations.

- The user interaction is a request-response interaction so we want to **measure availability**.

- **Measuring Availability**:

    - Proportion of status codes 2xx / total status codes.

- **Filtering**:

    - If we want profile page requests from HTTP we want to scrape the following: `/profile/user/*`

    - **Further Refining**:

        - Find the proportion HTTP `GET` requests for `/profile/user/*` that have a `Response Code` of `200` or `300` or `400`.

    - **Final SLI Location / Metric**:

        - The proportion of HTTP `GET` requests for `/profile/user` or `/profile/user/avatar` that have `200`, `300` or `400` response codes measured at the `load balancer`.

    - **Latency SLI**:

        - Look for **entire responses** sent in < X-ms (milliseconds) at the load balancer.



<h3>Observability Gaps</h3>

**Issues**:

- Measuring at the front end (for revenue purposes) only gives you protocol metadata. This only gives you partial visibility into your overall infra.

**Reflection Questions**:

- Do the SLIs capture the entire user journey and failures?

- What are the edge cases and exceptions?

- Do the SLIs capture all journey permutations?


<h4>Observability Issue w/ Previous Example</h4>

**Resolution**:

- Create a *probe* that:
    1. Inspects the *HTTP* response body.

    2. Validates load balancer routes.

    3. Tests CDN serving data.



<h3>Back-End Infrastructure Failure</h3>

**System Analysis**:

    - Ignore the front end SLIs since we know these have visibility issues.

    - Ignore the client side (CDN) since it's only used for ads and analytics.

    - Focus on the core backend infrastructure.

*Focus*:

    - Focus on the load in the load balancer and server pools.

Identify elevated latency and `500` errors.

*Focus*:

    - Focus on "bad code" being push into production.

This bad code would have to get past **(i)** the `OK` response header and **(ii)** the prober checking the `response body`.

*Focus*:

    - Focus on a middle ground solution. You don't have to send them a failure code if you can't get the leaderboard to send.

    - You can serve the user a partial response in the event of a failure to lookup.

*Focus*:

    - The prober (sitting at the front end) won't catch the case where the wrong user profile is served.

    - This is a huge issue and you'd want to measure this.



<h3>Setting Achieveable SLO Targets</h3>

*Focus*:

    - Find all possible risks.

    - Estimate their cost to the error budget.

    - If total cost > error budget (SLO targets) need to follow Pareto Principle and solve the most vital ones first.



<h3>Documenting the SLO</h3>

Everyone in your organization working on the *service*, *product managers*, *developers*, *SREs* and *executives* needs to know the following:

    - Where the line is (SLO/SLI)

    - What happens if it's crossed (SLA)

    - Exceptions to standard measuring procedures.

    - Set owners for each SLO.

There should be historical data on previous SLOs and documentation to show why each SLO was changed.

*Example*: Don't count `503` as errors because load balancer handles them.

The status (development, staging, paging) of the SLO should always be tracked.

Capture all of your metadata in one place in the version controlled *configuration file* to have a "single source of truth".



<h3>SRE Service Dashboard</h3>

**[Example Google Dashboard](https://imgur.com/a/Uqhlpd6)**

*Important Metadata Features*:

    - Measurement Window: Defines the time period of each data "chunk measured" before restart.

    - Graph Duration: The time period that a SRE would see on their dashboard GUI.

    - Target: The availability target that your want to maintain.

    - Owner: The person that owns the product or service. (Product Manager for example)

    - Contacts: Top down contacts on the product/service. (Tech Lead -> SRE)

    - Status: The status of the service.

    - Rationale: What results from the SLI being triggered. (The negative externalities)

    - References: Links to any relevant internal notes around this service.

    - Changelog: A record of any changes made to the service's SLI/SLO.



<h3>Characteristics of an Error Budget Policy</h3>

*7 Characteristics*:

    - Result in engineering efforts to improve reliability if error budget is spent.

    - Quantitatively describe WHEN the error budget will kick in.

    - Quantitatively describe HOW the error budget will kick in. Specifically how the Dev/SRE teams will respond.

    - Set in place consequences if the SLO consistently fails over a long time horizon.

    Example: devs that sacrifice reliability for features should be let go.

    - Policy is a consistently applied set of rules.

    - Document who disagreements get escalated to.

    - Policy should be agreed upon and signed off by all parties.



<h3>How to Create an Error Budget Policy</h3>

*Steps*:

    - Increase consequences w.r.t. increased levels of error budget burn.

    - Developers and SREs must work together to push out new features to the user while mainting the reliability. This is impossible to do all the time so it's a constant balance.

    - SREs want to help the SWEs develop more features safely. Want to have alligened incentives. This is the only way that SRE will work in an organization.

    - Examples consequence: Pulling back the reins on feature releases. This will be a consequence to the devs for breaking the codebase which annoyed the users.



<h3>Example Error Budget Policy</h3>

*Threshold 1*:

    - Automated alerts are setup to notify the SRE of an SLO that is at risk.

*Threshold 2*:

    - SREs decide they need collaborative efforts to defend the SLO. Devs will come in to assist.

*Threshold 3*:

    - 30-day error budget has been spent without a root cause figured out. All feature releases will be blocked as a consequence and dev will need to interlock with SRE to fix the issue.

    - The changes are bundled up into a weekly hotfix patch.

    - DO NOT cut a new release from the devlopment branch.

*Threshold 4*:

    - 90-day error budget has been spent without a root cause figured out. SRE will escalate to executive leadership to get more engineering time dedicated to this issue at hand.



<h3>Example Policy Scenario</h3>

*Scenario*:

    - Availability SLO: 99.9% over past 30 days.

    - Bug causes 99.85% availability (0.15% of the time serves errors)

*Resolution*:

    - See visual example **[here](https://imgur.com/a/QWzdLxz)**