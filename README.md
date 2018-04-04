# statistics
New Statistics

# Statistics

The Mailjet API allows you to retrieve statistics on your sendings. Several endpoints have been designed for this purpose:

* [Key Performance Statistics](#key-performance-statistics)
* [Statistics Per Contact](#contact-statistics)
* [Clicked Links Statistics](#stats-for-clicked-links)
* [Mailbox Providers Statistics](#mailbox-providers-metrics)
* [Geographical Statistics](#geographical-statistics)
* [Additional Stats Resources](#additional-stats-resources)

## Migration Guide

After March 15th the API endpoints used for retrieving stats were revamped in a major way. Stats for any account migrated or created after March 15th 2018 can be retrieved with the newly created endpoints.

The main improvements of the new system include:

- Streamlined API endpoints, combining several legacy resources into one to ease the retrieving of key performance stats
- Detailed stats on clicked links, including number of unique and total clicks, URL position etc.
- Statistics based on Mailbox providers, which allow you to easily identify issues with deliverability / engagement based on the recipients' mailbox providers

| Legacy statistics endpoints | New statistics endpoints |
|---|---|
| [`/apikeytotals`](/email-api/v3/apikeytotals/)  | [`/statcounters`](/email-api/v3/statcounters/) |
| [`/domainstatistics`](/email-api/v3/domainstatistics/) | [`/statistics/recipient-esp`](/email-api/v3/statistics/recipient-esp/) |
| [`/graphstatistics`](/email-api/v3/graphstatistics/) | [`/statcounters`](/email-api/v3/statcounters/) |
| [`/listrecipientstatistics`](/email-api/v3/listrecipientstatistics/) | [`/statcounters`](/email-api/v3/statcounters/) |
| [`/liststatistics`](/email-api/v3/liststatistics) | [`/statcounters`](/email-api/v3/statcounters/) |
| [`/openstatistics`](/email-api/v3/openstatistics/) | [`/statcounters`](/email-api/v3/statcounters/) |
| [`/toplinkclicked`](/email-api/v3/toplinkclicked/) | [`/statistics/link-click`](/email-api/v3/statistics/link-click/) |

In this guide we will focus on resources that are available for new / migrated users.

## Key Performance Statistics

The <code>[/statcounters](/email-api/v3/statcounters/)</code> resource is a multifunctional tool that allows you to view stats through various prisms while varying the Source (API Key, Campaign or List), the Timing (Event-based or Message-based counters' timestamp), or the Timeframe (Lifetime, Day, Hour, Minute).

The response will provide statistics that you can display in different ways:

* [Delivery Rates Statistics](#delivery-rates-statistics)
* [Contact Engagement Statistics](#contact-engagement-statistics)
* [Additional Metrics](#additional-metrics)

### Stats at Campaign, List or APIKey Level

The [`/statcounters`](/email-api/v3/statcounters/) code samples available in the following sections are done at a campaign level, which is indicated by the use of the following filters in the calls:

- `SourceId=$Campaign_ID` - Substitute `$CampaignID` with the ID of the Campaign you are interested in.
- `CounterSource=Campaign`

If you want to retrieve these key statistics but at a **List** level, make the same request but use the **$ListID** as the value of the <code>SourceID</code> filter and enter **List** as the <code>CounterSource</code>.

Similarly, if you need the stats at an **Account** level, make the request with `ApiKey` as the <code>CounterSource</code> value. Keep in mind that you can only retrieve data for the ApiKey with which you are authenticated.

### Event-based vs Message-based Stats Timing

The [`/statcounters`](/email-api/v3/statcounters/) resource allows you to retrieve information both based on the message sending time (message-based) and on the timing of the event occurrence (event-based).

Message-based stats allow you to easily view the success of your sending by having the delivery rates / contact engagement details linked to the sending time.

Event-based stats allow you to view the spread of events over time after the initial sending, helping you identify when recipients were most active / engaged with your campaigns.

**Example:** A campaign is sent on Day1. There are 10 opens on Day2 and another 20 on Day3. If you use `CounterTiming=Message` in the call, the returned result will be for the messages that were opened, thus showing 30 opens on Day1. If you use `CounterTiming=Event`, [`/statcounters`](/email-api/v3/statcounters/) will return the information on the open events, showing 10 opens on Day2 and 20 on Day3.

To specify which details you need, use the `CounterTiming` filter.

- `CounterTiming=Message` - retrieves the stats based on the message sending time.
- `CounterTiming=Event` - retrieves details based on the event occurrence.

<div></div>

### Delivery Rates Statistics

Delivery rate statistics are often the first metrics you want retrieved for your campaigns and Lists.

In this section of the guide, we will explain which call you need to make to retrieve and calculate the key performance indicators displayed in your Mailjet user interface.

![delivery_rates](../images/stats-delivery-rates.png)

The most suitable endpoint to retrieve delivery rates statistics is <code>[/statcounters](/email-api/v3/statcounters/)</code>.

By setting appropriate value for the <code>CounterSource</code>, <code>CounterResolution</code>, and <code>CounterTiming</code> filters you can generate an array of responses allowing to calculate the campaign statistics [as displayed in your Mailjet account](https://www.mailjet.com/docs/statistics#stats-overview).

Use the following filter values:

`SourceID=$CampaignID`

`CounterSource=Campaign`

`CounterResolution=Lifetime`

`CounterTiming=Message`


```shell
# View : Retrieve Key Delivery statistics for a Specific Campaign
curl -s \
	-X GET \
	--user "$MJ_APIKEY_PUBLIC:$MJ_APIKEY_PRIVATE" \
	GET https://api.preprod.mailjet.com/v3/REST/StatCounters?SourceId=$Campaign_ID&CounterSource=Campaign&CounterTiming=Message&CounterResolution=Lifetime \
```

> API response:

```json
{
    "Count": 1,
    "Data": [
        {
            "APIKeyID": 123456,
            "EventClickDelay": 322,
            "EventClickedCount": 6,
            "EventOpenDelay": 739,
            "EventOpenedCount": 11,
            "EventSpamCount": 0,
            "EventUnsubscribedCount": 2,
            "EventWorkflowExitedCount": 0,
            "MessageBlockedCount": 12,
            "MessageClickedCount": 3,
            "MessageDeferredCount": 0,
            "MessageHardBouncedCount": 5,
            "MessageOpenedCount": 8,
            "MessageQueuedCount": 0,
            "MessageSentCount": 15,
            "MessageSoftBouncedCount": 0,
            "MessageSpamCount": 0,
            "MessageUnsubscribedCount": 2,
            "MessageWorkFlowExitedCount": 0,
            "SourceID": 654321,
            "Timeslice": "",
            "Total": 32
        }
    ],
    "Total": 1
}
```

The rules you might require to calculate / retrieve the respective statistics can be found in the below table:

<table class="tg">
  <tr>
    <th class="tg-baqh" colspan="3"><strong><center>Delivery Rates</strong></center></th>
  </tr>
  <tr>
    <td class="tg-yw4l"><i>Stat</i></td>
    <td class="tg-yw4l"><i>Description</i></td>
    <td class="tg-yw4l"><i>Equals</i></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Email Total</td>
    <td class="tg-yw4l">The total number of processed emails for the campaign.</td>
    <td class="tg-yw4l"><code>Total</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delivered count</td>
    <td class="tg-yw4l">The number of emails that were successfully sent by Mailjet and accepted by the recipient’s server.</td>
    <td class="tg-yw4l"><code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Delivered</td>
    <td class="tg-yw4l">Delivered emails as a percentage of processed emails.</td>
    <td class="tg-yw4l"><code>MessageSentCount</code> / <code>Total</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Blocked</td>
    <td class="tg-yw4l">Blocked emails as a percentage of processed emails.</td>
    <td class="tg-yw4l"><code>MessageBlockedCount</code> / <code>Total</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Soft-bounced</td>
    <td class="tg-yw4l">Soft-bounced emails as a percentage of processed emails.</td>
    <td class="tg-yw4l"><code>MessageSoftBouncedCount</code> / <code>Total</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Hard-bounced</td>
    <td class="tg-yw4l">Hard-bounced emails as a percentage of processed emails.</td>
    <td class="tg-yw4l"><code>MessageHardBouncedCount</code> / <code>Total</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Retrying</td>
    <td class="tg-yw4l">Retrying emails as a percentage of processed emails.</td>
    <td class="tg-yw4l"><code>MessageDeferredCount</code> / <code>Total</code></td>
  </tr>
</table>

### Contact Engagement Statistics

When you aim to analyze the quality of your contacts lists, you may want to retrieve your Contact Engagement Statistics on a specific campaign.

That's exactly the same <code>[/statcounters](/email-api/v3/statcounters/)</code> request as when retrieving Key Delivery Statistics, but you will look at the metrics through a different prism.

![contact_engagement](../images/stats-contact-engage.png)

By setting appropriate value for the <code>CounterSource</code>, <code>CounterResolution</code>, and <code>CounterTiming</code> filters you can generate an array of responses allowing to calculate the campaign statistics [as displayed in your Mailjet account](https://www.mailjet.com/docs/statistics#stats-overview).

Use the following filters values:

`SourceID=$CampaignID`

`CounterSource=Campaign`

`CounterResolution=Lifetime`

`CounterTiming=Message`

```shell
# View : Retrieve Contact Engagement Statistics for a Specific Campaign
curl -s \
	-X GET \
	--user "$MJ_APIKEY_PUBLIC:$MJ_APIKEY_PRIVATE" \
	GET https://api.preprod.mailjet.com/v3/REST/StatCounters?SourceId=$Campaign_ID&CounterSource=Campaign&CounterTiming=Message&CounterResolution=Lifetime \
```

> API response:

```json
{
    "Count": 1,
    "Data": [
        {
            "APIKeyID": 123456,
            "EventClickDelay": 322,
            "EventClickedCount": 6,
            "EventOpenDelay": 739,
            "EventOpenedCount": 11,
            "EventSpamCount": 0,
            "EventUnsubscribedCount": 2,
            "EventWorkflowExitedCount": 0,
            "MessageBlockedCount": 12,
            "MessageClickedCount": 3,
            "MessageDeferredCount": 0,
            "MessageHardBouncedCount": 5,
            "MessageOpenedCount": 8,
            "MessageQueuedCount": 0,
            "MessageSentCount": 15,
            "MessageSoftBouncedCount": 0,
            "MessageSpamCount": 0,
            "MessageUnsubscribedCount": 2,
            "MessageWorkFlowExitedCount": 0,
            "SourceID": 654321,
            "Timeslice": "",
            "Total": 32
        }
    ],
    "Total": 1
}
```

In the below table you will find the rules to retrieve and calculate the respective statistics.

<table class="tg">
  <tr>
    <th class="tg-amwm"colspan="3"><strong><center>Contact Engagement</strong></center></th>
  </tr>
  <tr>
    <td class="tg-jogk"><i>Stat</i></td>
    <td class="tg-jogk"><i>Description</i></td>
    <td class="tg-jogk"><i>Equals</i></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Opened Count</td>
    <td class="tg-yw4l">The unique email opens based on the total number of delivered emails.</td>
    <td class="tg-yw4l"><code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Opened</td>
    <td class="tg-yw4l">Unique opens as a percentage of the total number of delivered emails</td>
    <td class="tg-yw4l"><code>MessageOpenedCount</code> / <code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Clicked Count</td>
    <td class="tg-yw4l">The number of opened emails that have at least one click. This does not include any unsubscribe clicks.</td>
    <td class="tg-yw4l"><code>MessageClickedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Clicked</td>
    <td class="tg-yw4l">Clicked emails as a percentage of the total number of opened emails</td>
    <td class="tg-yw4l"><code>MessageClickedCount</code> / <code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Unsubscribed Count</td>
    <td class="tg-yw4l">Number of unsubscriptions</td>
    <td class="tg-yw4l"><code>MessageUnsubscribedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Unsubscribed</td>
    <td class="tg-yw4l">Unsubscriptions as a percentage of delivered emails</td>
    <td class="tg-yw4l"><code>MessageUnsubscribedCount</code> / <code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Spam Count</td>
    <td class="tg-yw4l">Number of messages marked as Spam</td>
    <td class="tg-yw4l"><code>MessageSpamCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">% Marked as Spam</td>
    <td class="tg-yw4l">Emails marked as Spam as a percentage of delivered emails</td>
    <td class="tg-yw4l"><code>MessageSpamCount</code> / <code>MessageSentCount</code></td>
  </tr>
</table>

### Evolution / Graph Statistics

By using <code>[/statcounters](/email-api/v3/statcounters/)</code> and setting appropriate values for the <code>CounterSource</code>, <code>CounterResolution</code>, and <code>CounterTiming</code> filters you can generate an array of responses over a period of time. With this information you will be able to see the evolution of the campaign events over the selected time period.

![stats_graph](../images/stats-campaign-graph.png)

Use the following filter settings:

`CounterSource=Campaign`

`CounterResolution=Day`

`CounterTiming=Event`

The response will produce results for a campaign, for message counters, with a daily time slice. You can use the response results to generate a graph similar to the one available in the Stats Dashboard on the front-end.

```shell
# View : View campaign evolution statistics, based on daily timeslices and with a defined timeframe
curl -s \
	-X GET \
	--user "$MJ_APIKEY_PUBLIC:$MJ_APIKEY_PRIVATE" \
	https://api.mailjet.com/v3/REST/StatCounters?SourceId=$CAMPAIGN_ID&CounterSource=Campaign&CounterResolution=Day&CounterTiming=Event&FromTs=123&ToTs=456
```

> API response:


```json
{
  "StatCounters": {
    "Count": 1,
    "Data": [
        {
        "APIKeyID": "320046",
        "EventClickDelay": "200",
        "EventClickCount": "3",
        "EventOpenDelay": "20",
        "EventOpenedCount": "4",
        "EventSpamCount": "4",
        "EventUnsubscribedCount": "5",
        "EventWorkflowExitedCount": "5",
        "MessageBlockedCount": "7",
        "MessageClickedCount": "3",
        "MessageDeferredCount": "2",
        "MessageHardBouncedCount": "5",
        "MessageOpenedCount": "5",
        "MessageQueuedCount": "3",
        "MessageSentCount": "2",
        "MessageSoftBouncedCount": "7",
        "MessageSpamCount": "5",
        "MessageUnsubscribedCount": "1",
        "MessageWorkflowExitedCount": "8",
        "SourceID": "123456789",
        "Timeslice": "456",
        "Total": "50000",
        }
        {
        "APIKeyID": "320046",
        "EventClickDelay": "113",
        "EventClickCount": "2",
        "EventOpenDelay": "15",
        "EventOpenedCount": "2",
        "EventSpamCount": "0",
        "EventUnsubscribedCount": "1",
        "EventWorkflowExitedCount": "2",
        "MessageBlockedCount": "3",
        "MessageClickedCount": "1",
        "MessageDeferredCount": "2",
        "MessageHardBouncedCount": "2",
        "MessageOpenedCount": "2",
        "MessageQueuedCount": "3",
        "MessageSentCount": "2",
        "MessageSoftBouncedCount": "7",
        "MessageSpamCount": "5",
        "MessageUnsubscribedCount": "1",
        "MessageWorkflowExitedCount": "8",
        "SourceID": "123456789",
        "Timeslice": "123",
        "Total": "50000",
        }
    ]
  },
		"Total": 2
}
```

Using multiple such calls with different CampaignID values for the `SourceID` filter, then overlaying them on the same graph, will allow you to compare the evolution of the different campaigns. Comparing campaigns in such a fashion is useful when evaluating their performance.

<div></div>

### Additional metrics

Using the same <code>[/statcounters](/email-api/v3/statcounters/)</code> resource, if you want to dig a little deeper, you will be able to get more detailed metrics.

They can help with thoroughly analyzing your contacts engagement indicators.

![additional_metrics](../images/stats-additional-metrics.png)

See the below table for details on how to calculate the respective statistics.

<table class="tg">
  <tr>
    <th class="tg-amwm" colspan="3"><strong><center>Additional metrics</strong></center></th>
  </tr>
  <tr>
    <td class="tg-jogk"><i>Stat</i></td>
    <td class="tg-jogk"><i>Description</i></td>
    <td class="tg-jogk"><i>Equals</i></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Total Opens</td>
    <td class="tg-yw4l">The total amount of times a marketing campaign email has been opened. This includes instances when an already read message is opened again.</td>
    <td class="tg-yw4l"><code>EventOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Messages Opened</td>
    <td class="tg-yw4l">The number of unique marketing emails that were opened by the recipients.</td>
    <td class="tg-yw4l"><code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Average opens per message</td>
    <td class="tg-yw4l">How many times on average the recipient opens a campaign message.</td>
    <td class="tg-yw4l"><code>EventOpenedCount</code> / <code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delivered message opened</td>
    <td class="tg-yw4l">The percentage of delivered messages that were opened.</td>
    <td class="tg-yw4l"><code>MessageOpenedCount</code> / <code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Average open delay</td>
    <td class="tg-yw4l">Average time spent between the message being delivered and it being opened.</td>
    <td class="tg-yw4l"><code>EventOpenDelay</code> / <code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Total clicks</td>
    <td class="tg-yw4l">The number of times any link in the campaign email was clicked.</td>
    <td class="tg-yw4l"><code>EventClickedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Messages clicked</td>
    <td class="tg-yw4l">Number of messages that had a link clicked.</td>
    <td class="tg-yw4l"><code>MessageClickedCount</td>
  </tr>
  <tr>
    <td class="tg-yw4l">Average clicks per message</td>
    <td class="tg-yw4l">How many times on average a contact clicks on a link for each clicked message.</td>
    <td class="tg-yw4l"><code>EventClickedCount</code> / <code>MessageClickedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Open messages clicked</td>
    <td class="tg-yw4l">Percentage of opened messages that received a click.</td>
    <td class="tg-yw4l"><code>MessageClickedCount</code> / <code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delivered messages clicked</td>
    <td class="tg-yw4l">The percentage of delivered messages that were clicked.</td>
    <td class="tg-yw4l"><code>MessageClickedCount</code> / <code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Average click delay</td>
    <td class="tg-yw4l">Average time spent between opening an email and clicking on a link in it.</td>
    <td class="tg-yw4l"><code>EventClickDelay</code> / <code>MessageOpenedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Total unsubscribed</td>
    <td class="tg-yw4l">Total number of unsubs for the campaign.</td>
    <td class="tg-yw4l"><code>EventUnsubscribedCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delivered messages unsubscribed</td>
    <td class="tg-yw4l">Unsubscribe events as a percentage of delivered messages</td>
    <td class="tg-yw4l"><code>EventUnsubscribedCount</code> / <code>MessageSentCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Total marked as spam</td>
    <td class="tg-yw4l">Unsubscribe events as a percentage of delivered messages</td>
    <td class="tg-yw4l"><code>EventSpamCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delivered messages marked as spam</td>
    <td class="tg-yw4l">Spam events as a percentage of delivered messages</td>
    <td class="tg-yw4l"><code>EventSpamCount</code> / <code>MessageSentCount</code></td>
  </tr>
</table>

## Statistics for Specific Recipient

The Mailjet API allows you to easily access statistics for a specific recipient. This is useful when you need to review the delivery and engagement indicators for specific contacts.

![recipient_stats](../images/stats-contact.png)

Use [`/contactstatistics`](/email-api/v3/contactstatistics/) to retrieve the respective information:

{{contactstatistics_GET}}

> API response:

{{contactstatistics_response}}

<div></div>

## Stats for Clicked Links

Clicked links can help optimize you email engagement rate by showing you how different Sections, images or Calls-to-action affect how your recipients interact with your emails.

![stats_clicked_links](../images/stats_linkclick.png)

As a result, you may want to use [`/statistics/link-click`](/email-api/v3/statistics/link-click/) to retrieve activity information based on the links in your campaign templates. With this endpoint you can track both unique clicks and total click events, as well as retrieve the URL and its position within the template. It gives you valuable insight into what links are used more often than others, possibly showing correlation between position / design and link popularity.

```shell
# View : View statistics for total and unique clicks for each clicked URL in a campaign email
curl -X GET \
  'https://api-priv.preprod.mailjet.com/v3/REST/statistics/link-click?CampaignID=$CampaignID' \
  -H 'authorization: Basic NDQ0Y2YxZWY0ZGZjNzliNTE3N2E5YzdjZTEyNWY0ODA6ZGQyYTExZjY2ODE2MTljOTBjYWNkZWQ3MWQ1MmJjZTg=' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
```  


> API response:

```json  
  {
    "Count": 1,
    "Data": [
        {
            "URL": "https://www.google.fr/",
            "PositionIndex": 1,
            "ClickedMessagesCount": 2,
            "ClickedEventsCount": 2
        }
    ],
    "Total": 1
}
```

<div></div>

## Mailbox Provider Statistics

The mailbox provider statistics highlight how your emails perform across all the major providers you are sending to - Hotmail, Yahoo, Gmail etc.

In this section of the guide, we will explain which call you need to make to retrieve and calculate the key performance indicators displayed in your Mailjet user interface.

![mailbox_providers](../images/stats-providers.png)

The [`/statistics/recipient-esp`](/email-api/v3/statistics//recipient-esp) resource can be used to view statistics based on the Email Service Providers of the recipients of your campaign.

You must provide a `$CampaignID` in the `Campaign` filter in order to retrieve data.


```shell
# View : View delivery and contact engagement statistics for a campaign across different Mailbox providers
curl -X GET \
  'https://api-priv.preprod.mailjet.com/v3/REST/statistics/recipient-esp?campaignID=$CampaignID' \
  -H 'authorization: Basic NDQ0Y2YxZWY0ZGZjNzliNTE3N2E5YzdjZTEyNWY0ODA6ZGQyYTExZjY2ODE2MTljOTBjYWNkZWQ3MWQ1MmJjZTg=' \
  -H 'cache-control: no-cache' \
```  

> API response:

```json  
  {
    "Count": 1,
    "Data": [
        {
            "ESPName": "others",
            "DeliveredMessagesCount": 3,
            "AttemptedMessagesCount": 3,
            "OpenedMessagesCount": 3,
            "ClickedMessagesCount": 2,
            "DeferredMessagesCount": 0,
            "SoftBouncedMessagesCount": 0,
            "HardBouncedMessagesCount": 0,
            "UnsubscribedMessagesCount": 0,
            "SpamReportsCount": 0,
            "OpenRate": 1,
            "ClickThroughRate": 0.6667,
            "SoftBouncedRate": 0,
            "HardBouncedRate": 0,
            "UnsubscribedRate": 0,
            "SpamReportsRate": 0,
            "DeferredRate": 0
        }
    ],
    "Total": 1
}
```

Below you can see how to calculate stats by ESP as displayed in the Email Providers section in the front-end, and which properties are used to calculate the stats.

<table class="tg">
  <tr>
    <th class="tg-amwm" colspan="3"><strong><center>Email Providers</strong></center></th>
  </tr>
  <tr>
    <td class="tg-jogk"><i>Stat</i></td>
    <td class="tg-jogk"><i>Description</i></td>
    <td class="tg-jogk"><i>Equals</i></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Email Provider</td>
    <td class="tg-yw4l">Name of the ESP</td>
    <td class="tg-yw4l"><code>ESPName</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Attempted</td>
    <td class="tg-yw4l">Number of messages attempted to be delivered to the ESP domain at least once. Excludes blocked messages</td>
    <td class="tg-yw4l"><code>AttemptedMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Open Rate</td>
    <td class="tg-yw4l">Messages a recipient in the ESP opened as a percentage of Attempted messages</td>
    <td class="tg-yw4l"><code>OpenedMessagesCount</code> / <code>AttemptedMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Click Rate</td>
    <td class="tg-yw4l">Messages a recipient in the ESP clicked as a percentage of Opened messages</td>
    <td class="tg-yw4l"><code>ClickedMessagesCount</code> / <code>OpenedMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Unsubscribed</td>
    <td class="tg-yw4l">Messages a recipient in the ESP unsubscribed as a percentage of delivered messages</td>
    <td class="tg-yw4l"><code>SoftBouncedMessagesCount</code> / <code>DeliveredMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Spam</td>
    <td class="tg-yw4l">Messages a recipient in the ESP marked as Spam as a percentage of delivered messages</td>
    <td class="tg-yw4l"><code>SpamReportsCount</code> / <code>DeliveredMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Soft-bounce</td>
    <td class="tg-yw4l">Messages that were soft-bounced from the ESP as a percentage of Attempted messages</td>
    <td class="tg-yw4l"><code>SoftBouncedMessagesCount</code> / <code>AttemptedMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Hard-bounce</td>
    <td class="tg-yw4l">Messages that were hard-bounced from the ESP as a percentage of Attempted messages</td>
    <td class="tg-yw4l"><code>HardBouncedMessagesCount</code> / <code>AttemptedMessagesCount</code></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Retrying</td>
    <td class="tg-yw4l">Messages that were deferred from the ESP as a percentage of Attempted messages. Mailjet will continue trying to send these messages in the next 5 days, after which, if not successfully delivered, they will become Soft-Bounced</td>
    <td class="tg-yw4l"><code>DeferredMessagesCount</code> / <code>AttemptedMessagesCount</code></td>
  </tr>
</table>

## Geographical Statistics

Geographical stats provide information on email opens and clicks, broken down by country. This helps you identify possible engagement issues with recipients from specific regions. With those details in mind, you can update your sendings to focus on countries that are performing well, or address issues with markets that are underperforming.

Use the [/geostatistics](/email-api/v3/geostatistics/) resource to get information on opens and clicks by country.

[code sample]

## Additional Stats Resources

The following statistic resources will allow you to view information about the events on your messages. They will show a log of events on your messages for a selected time period. By default, the payload response will include the log for the current day, but you can specify a timeframe with the `FromTS` and `ToTS` filters.

- [/openinformation](/email-api/v3/openinformation/) : Will give you details on opens, including useful information like timestamp for each open event, UserAgent, CampaignID and UserID.
- [/clickstatistics](/email-api/v3/clickstatistics/) : Shows information on click events, including timestamp for the click, URL, UserAgent and delay between sending and the click event.
- [/bouncestatistics](/email-api/v3/bouncestatistics/) : Displays details for bounces, including bounce timestamp, campaign ID and contact ID, whether bounce is permanent or not.
