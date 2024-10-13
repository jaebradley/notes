# [ScrollSets: A New Way to Store Knowledge](https://breckyunits.com/scrollsets.html)

* Approach is to store tabular information in a single, long, plain text file
* Only syntax characters necessary are spaces and newlines
* In effect, creating a long “scroll”

## Parsers

* Define and write parsers
* These parsers contain information about the thing they measure
* Only required information for these parsers is an identifier (like `temperature`)

## Concepts

* Concepts are made up of measurements
* Concepts need to have an identifier
* A line starting with an identifier “measurement” or declaration is the start of a new concept
* Comments can be attached to any measurement using an indentation

```
Example

id Earth
temperature 14
  > The global mean surface air temperature for that period was 14°C (57°F), with an uncertainty of several tenths of a degree.
  - NASA
   https://earthobservatory.nasa.gov/world-of-change/global-temperatures
```

* Edit history is tracked via `git`

## Enhancements

* Computed measures are measurements not stored statically, but derived at runtime from other measurements
* Generally want to add type attributes to measures
* Can nest measures and parsers - a min parser + temperature parser can generate a minimum temperature parser (and column name)
