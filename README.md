See the <a href="https://hello-world-f1c87.firebaseapp.com">Live Demo!</a>

## Usage:

data - JSON - required: The data that you want to render in your table
rowHeight - Number - required - The height of the row
header - JSON - required - The configuration for defining display order, sortable columns, data types, and data output transformations.

```vue
<template>
    <infinite-table
      :data="theData"
      rowHeight="48"
      :header="header">
      </infinite-table>
</template>
<script>
import InfiniteTable from 'vue-infinite-scroll-table'

export default {
    components: {
        InfiniteTable
    },
    data: () => ({
        yourData: yourData,
        header: [
            { key: 'firstColumnKey', label: 'First Column Label', sortable: true },
            { key: 'secondColumnKey', label: 'Some Date Column', type: Date },
            { key: 'thirdColumnKey', label: 'Some Image Column', type: 'image' }
            { key: 'fourthColumnKey', label: 'fourthColumnKey' }
        ]
    }),
}
</script>
```

### Header Config Rules

Each object in the header array will map to a value in the table.

Key, and Label are required, but type and sortable are optional.

Type is used for sorting on date values, and rendering images.

`sortable: true` will enable sorting by clicking on the column header.