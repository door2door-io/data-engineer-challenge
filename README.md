# Data Engineer challenge

door2door, the operator of the 'allygator shuttle' service in Berlin, collects the live position of all vehicles in its fleet in real-time via a GPS sensor in each vehicle. In order to handle these requests, door2door has an API handling the shifts and vehicle position updates during the ridepooling operations. This API takes care of handling our fleets and save information in its database. Before saving this data, the API emits event to a data stream from where another component saves it to a data lake.

For this exercise, we collected a sample of the data lake structure and put it on the `/data` folder.


See the [Data Mode](#data-model) section for more information.


## Part 1

Our BI team was requested to answer the following question to our stakeholders:

* What is the average  distance traveled by our vehicles during an operating period?

We would like to ask you to develop a solution that can collect information from the files found in `/data`, extract the main events that occurred during an operation, transform and store them in a way that is easy for our analysts to answer the question above.

You are free to use any combination of tools along with the necessary instructions for us to execute them. If your solution is setup to run locally, a containerized solution is required.


## Part 2

Due to regulation changes, our clients will have to provide break times for drivers to rest during the operations. During a rest, drivers are unavailable for rides but can still use their cars to move in the city. When vehicles move during a break they will still emit location updates, but those should not be used for the distance computation used in the question answered on `Part 2`.

The events that you worked with are sent from our API to the data lake used for data analysis.

Based on the way you modeled your solution in `Part 1`, propose a way of adapting the events so during the analysis the break times are taking in to consideration when the kilometers driven are calculated.

You can suggest changes in the existing ones and addition of new events if needed.


## Data Model

The events saved by the API follow at data model needed for the managing the ridepooling operation logic. This model is described below and is not necessarily the most suitable for the data analysis.

All events sent to the stream are JSON strings, of the form:

```json5
{
  "event": "create", /* see possible events for each entity below */
  "on": "some_entity",
  "at": "2019-05-19T16:02:02Z",
  "data": { /* ... */ },
  "organization_id": "id-of-organization-that-the-entity-belongs-to"
}
```

- `event` indicates the type of event - either an entity has been `create`d or `update`d.
- `on` indicates the entity type that the event occurred on - see sections below.
- `data` will be an object containing information about the entity that the event occurred on - see sections below.


## Events on `vehicle`

Possible events:
- `register` registers a vehicle
- `update` updates vehicles location
- `deregister` after this event occurs, the vehicle will never send further location updates .

When a vehicle event is emitted, the `data` field will look as follows:

```json5
{
  "id":            "s0m3-v3h1cl3-1D",
  "location":      {
    "lat":              50.1, /* can be null if vehicle is new and no location updates were received yet */
    "lng":              40.2,  /* can be null if vehicle is new and no location updates were received yet */
    "at":               "2016-02-02T01:00:00Z",  /* ISO8601 format. Can be null if vehicle is new and no location updates were received yet */
  }
}
```

## Events on `operating_period`

Possible events: `create`, `delete`

When an admin adds or removes hours of operation for a region, the `on` field will be set to `operating_period`, and the `data` field will look as follows:

```json5
{
  "id":              "s0m3-p3r1od-1D",
  "start":           "2016-12-30T18:00:00Z", // UTC time, ISO8601 format
  "finish":          "2016-12-31T02:00:00Z" // UTC time, ISO8601 format
}
```


## Technical assumptions

* Your solution is expected to be implemented using one of the following languages: Java or Python, but we would recommend you to use the latter, since it is the stack you would be using if when join door2door.
* You are free to make use of any framework or library you please, but you should justify your choice.


## Delivery of your solution

Please deliver your solution to us as a publicly accessible git repository, or in a ZIP file. The repository should contain full instructions for us to run the solution on our own machines.

If you are able to publicly host the solution somewhere (e.g. Heroku, AWS), make sure the correct access is provided to door2door reviewers.

## Reviewing

The following description will give you an understanding of how we review the code challenge. What matters to us is to learn how you write code and model solutions. For us it is more important to have an understandable project than a complex algorithm.

The criteria that we are looking for are the following:

- Documentation: Is the project and the code properly documented?
- Correctness: Is the task solved? Does the data provided is correctly ingested by solution? If there is anything missing, is the reason why it is missing documented?
- Technology: Which libraries or approaches are used? Do they make sense for the task? Justify why you've decided to use those technologies to solve the code challenge.
- Code quality: Is the code understandable and maintainable? What programming paradigm is being used? Is it implemented correctly?
- Tests: How is the project tested? Does the project contain system and unit tests? Is the entire project tested or just parts of it?
