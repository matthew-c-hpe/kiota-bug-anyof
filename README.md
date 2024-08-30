The bug relates to a property in the request body.

Given an item in the properties map:
- if using anyOf as schema type,
- if the number of items in that item's list is 2,
- and if at least one of those items is nullable (nullable: true),
- and if at least one of those items is NOT an "object" data type (EXCEPT if the other is "array"),
Then the Kiota autogeneration fails with the following critical error:

`````
crit: Kiota.Builder.KiotaBuilder[0]
      error generating the client: One or more errors occurred. (Sequence contains no matching element)

`````

Examples: FAIL
`````
1. 2 nullable items, non-object data types
anyOf:
- type: string
  nullable: true
- type: integer
  format: int64
  nullable: true

2. 2 items, 1 of which is nullable, non-object data types
anyOf:
- type: string
  nullable: true
- type: integer
  format: int64

3. 2 items, both arrays
anyOf:
- type: array
  nullable: true
- type: array
  nullable: true
`````

Examples: SUCCESS
`````
1. 1 item
anyOf:
  - type: string
    nullable: true

2. Greater than 2 items
anyOf:
  - type: string
    nullable: true
  - type: integer
    format: int64
    nullable: true
  - type: boolean
    nullable: true


3. 2 items, both object data type
anyOf:
- type: object
  nullable: true
- type: object
  nullable: true

4. 2 items, one object, one array data type
- type: object
  nullable: true
- type: array
  nullable: true
`````
