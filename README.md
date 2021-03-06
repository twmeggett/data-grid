# Vue-Data-Grid

> A Vue 2 Vuex Firebase Data Grid Demo -
Live demo here https://twmeggett.github.io/data-grid/

## Build & Dev Setup

``` bash
# install dependencies
yarn install

# serve with hot reload at localhost:8080
yarn run dev
```

This is an example of a portable Vue data grid component.
The demo page is hooked up to a Firebase database, managing the data passed into the component to be displayed.

#Vue Component Example
```
<vue-table
  :columns="columns"
  :rows="payments"
  :showFilters="true"
  :showPagination="true"
  tBodyHeight="350px"
  :containerStyle="{ width: mobileSize ? '100%' : '700px' }">
  <template slot="Date" slot-scope="slotProps"><span>{{displayDate(slotProps.colData)}}</span></template>
  <template slot="Amount" slot-scope="slotProps"><span>{{usDollarFormatter(slotProps.colData)}}</span></template>
</vue-table>
```

#Props Passed to Component
```
columns: [
  {
    header: 'Name',
    accessor: 'Name',
    widthPercent: 20,
    sortMethod: function (a, b) { // custom sort by last name
      if (a['Name'].split(' ')[1] > b['Name'].split(' ')[1]) { return 1 }
      if (a['Name'].split(' ')[1] < b['Name'].split(' ')[1]) { return -1 }
      return 0
    }
  },
  {
    header: 'Amount',
    accessor: 'Amount',
    widthPercent: 20,
    customCell: true
  },
  {
    header: 'Description',
    accessor: 'Description',
    widthPercent: 40,
    editable: true,
    onChange: (payment) => {
      this.updatePaymentDesc(payment)
    }
  },
  {
    header: 'Date',
    accessor: 'Date',
    widthPercent: 20,
    customCell: true,
    filterMethod: function (rowValue, filterValue) { // custom filter on what the display date looks like MM/DD/YYYY
      const re = new RegExp(String(filterValue), 'i')
      const dateFormatted = moment(rowValue).format('MM/DD/YYYY')
      return String(dateFormatted).search(re) >= 0
    }
  }
]

rows // from Vuex store connected to Firebase
```
## Sorting

-Columns are sortable by default by clicking on the column name in the header.

-The name column sorts by last name using a custom sort function passed to the component

## Filtering

-Columns are filterable by entering text into the text inputs below the column name.

-The Data column filters by the displayed data (MM/DD/YYYY) instead of the timestamp by using a custom filter method passed to the component

## Searching

-All of the data is searchable by entering text into the search bar

-Data will still be sorted and filtered correctly

## Editing

-The Description column is an example of an editable column on click

-Press enter or click away from the input after editing the description to have the passed in onChange function update the data

-Press escape to reset the description to it's previous value

-Changed data is saved to the firebase database

## Custom Cell Template

-Both Date and Amount columns have custom templates passed in to properly format the data  
