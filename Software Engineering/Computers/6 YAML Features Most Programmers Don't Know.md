# [6 YAML Features Most Programmers Don't Know](https://www.redfin.com/MA/Quincy/150-Hancock-St-02171/unit-202/home/173411376?utm_source=ios_share&utm_medium=share&utm_campaign=copy_link&utm_nooverride=1&utm_content=link)

* `language: no` is interpreted as `language: false`
  * Need to write `"no"
* `013` is mapped to `11` as the leading zero triggers octal notation
* `4:30` is mapped to `270` as it is interpreted as a duration (`4 x 60 + 30`)
* The `|` can be used for a multi-line string like

```yaml
some_field: |
    blahblahblah
    hahahahaha
```

* This is equivalent to the following in `JSON`

```json
{
  "some_field": "blahblahblah\nhahahahaha"
}
```

* The leading whitespace is ignored and the first line determines the number of ignored leading whitespace
* The `&` allows the definition of a variable and the `*` indicates that the name of a variable follows

```yaml
email: &emailAddress "some@example.com"
id: *emailAddress
```

* The `emailAddress` variable is defined and set a value `"some@example.com"`
* The `id: *emailAddress` then references this value
* Can also define mappings using this pattern

```yaml
foo: &settings
  some:
    example: settings
  bar: baz
blah: *settings
jae: *settings
```

* There is also the merge key operator, `<<`, which merges a mapping into an existing mapping

```yaml
foo: &settings
  some:
    example: settings
  bar: baz
blah: 
  <<: *settings
  test: abcde
jae: *settings
```

* `blah` would be the key for an object that would look like

```json
{
  "some": {
    "example": "settings"
  },
  "bar": "baz",
  "test: "abcde"
}
```
