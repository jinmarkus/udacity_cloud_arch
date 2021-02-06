<!-- README

easiest way to view this file is in visual studio code or similar IDE that recognizes markdown, or in your web browser; formatting will update automatically

-->

# estimates

## questions

Write a paragraph or two describing the achievable Recovery Time Objective (RTO) and Recovery Point Objective (RPO) for this Multi-AZ, multi-region database in terms of:

1. Minimum RTO for a single AZ outage
    - 0 min
1. Minimum RTO for a single region outage
    - 0 - 5 min
1. Minimum RPO for a single AZ outage
    - 0
1. Minimum RPO for a single region outage
    - 1 - 5 min


## response

This is a difficult question to answer in a single answer.

The short answer: it depends

The long answer:

1. We have a multi-AZ database set up
    - This implies that a single AZ can go down and your down time would be negligible as the other AZ's database would be still active
    - RTO would be 0 ideally if only 1 AZ went down
1. We have a multi-Region database set up as well
    - The primary region is resonsible for queries, but should it go down, the backup region would kick in after an alert has triggered and serve as the primary database
    - If you modified the read replica database to also write, you could have it serve as your primary database for all intents and purposes
    - Once your primary database comes up, that could then serve as your backup (or secondary database) should the new primary go down
    - RTO would be as short or as long as your alert time
      - This could get expensive if you had something short ~1 min, ~5 min
      - assume 5 minutes for the sake of this exercise
    - If you can afford the autmation elements of these services, you could have it kick in automatically for you
    - If you're relying on a manual change from one database to another, now you're accoutning for human error which could add a factor of x4 to your time estimate (assuming you have good-and-ready documentation)
1. If your RTO is 0 for your single AZ in a multi-AZ setup, then no data will ever be lost; that, and you always have a synched database in another AZ working around the clock
    - therefore: RPO = 0 for single-AZ outage
1. If your system is automated and kicks over to the next region (no human error) as an alert occurs, the RPO would be the maximum amount of time between alerts (or less than that)