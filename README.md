See the <a href="https://hello-world-f1c87.firebaseapp.com">Live Demo!</a>

## Usage:

data - JSON - required: The data that you want to render in your table<br/>
rowHeight - Number - required - The height of the row<br/>
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
            { key: 'firstColumnKey', label: 'First Column Label', sortable: true, width: 100 },
            { key: 'secondColumnKey', label: 'Some Date Column', type: Date, width: 200 },
            { key: 'thirdColumnKey', label: 'Some Image Column', type: 'image', width: 200 }
            { key: 'fourthColumnKey', label: 'fourthColumnKey', width: 100 }
        ]
    }),
}
</script>
```

### Header Config Rules

Each object in the header array will map to a value in the table.<br/>

Key, and Label are required, but type, width and sortable are optional.<br/>

Width is optional, but recommended. It defines the width of your column so that it doesn't flex to fit the content.

Type is used for sorting on date values, and rendering images.<br/>

`sortable: true` will enable sorting by clicking on the column header.