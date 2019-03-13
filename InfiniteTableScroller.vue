<template>
  <div class="infinite-table-border">
    <div class="infinite-table" :style="{ width: tableWidth ? tableWidth + 'px' : 'auto', maxWidth: '100%', height: tableHeight}">
      <div class="scroll-tricker" :style="{ height: totalHeight + 'px' }"></div>
      <section ref="table">
      </section>
      <p class="no-data" v-if="!data.length">No results found. Please refine your search.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: ['data', 'rowHeight', 'header', 'tableHeight'],
  data: () => ({
    firstIndex: 0,
    offsetHeight: 0,
    headerEl: undefined,
    scroller: undefined,
    totalHeight: 0,
    sortKey: '',
    sortOrder: 0,
    tableWidth: 0
  }),
  watch: {
    data (val) {
      setTimeout(() => {
        this.renderChunk(this.fromPos, this.howMany)
      }, 0)
    }
  },
  computed: {
    computedData () {
      let results = JSON.parse(JSON.stringify(this.data))

      let config = this.header.find(val => { return val.key === this.sortKey })

      if (this.sortKey && this.sortOrder !== undefined) {

        results = results.sort((a, b) => {
          let first = a[this.sortKey]
          let second = b[this.sortKey]
          if (config.type && config.type === 'date') {
            first = new Date(first)
            second = new Date(second)
          }
          if (first === second) {
            return 0
          }
          if (first < second) {
            return this.sortOrder ? -1 : 1
          }
          if (first > second) {
            return this.sortOrder ? 1 : -1
          }
        })
      } 
      return results
    }
  },
  methods: {
    sortIcon (key) {
      if (key !== this.sortKey) {
        return ''
      } else if (key === this.sortKey) {
        return this.sortOrder ? '&uarr;' : '&darr;'
      }
    },
    sort (sortKey) {
      this.sortOrder = this.sortKey === sortKey ? !this.sortOrder : 1
      this.sortKey = sortKey
      this.headerEl = this.createHeader()
      this.renderChunk(this.fromPos, this.howMany)
    },
    createScroller () {
      return document.createElement("div")
    },
    createHeader () {
      let header = document.createElement('header')
      header.innerHTML = this.header.map(config => `
        <div class="col" ${ this.styleSetter(config) } ${ this.sortableAttributes(config) }>
          ${config.label + ' ' + (config.sortable ? this.sortIcon(config.key) : '') }
        </div>`).join('')
      return header
    },
    styleSetter ({ width, sortable  }) {
      return `style="min-width: ${width ? width + 'px' : 'unset'};cursor:${sortable ? 'pointer' : 'normal'};"`
    },
    sortableAttributes ({ sortable, key }) {
      return sortable ? `onclick="scroller.sort('${key}')" style="cursor:pointer;"` : '' 
    },
    transform (val, c) {
      if (c.transform && c.transform instanceof Function) {
        return c.transform(val)
      }
      if (c.type === 'image') {
        return `<img src="${val}" height="40px" style="background: grey;" />`
      }
      return val
    },
    generatorFn (val) {
      let row = document.createElement('div')
      row.classList.add('row')
      row.innerHTML = this.header.map((config) => `
        <div class="col">
          <span>${this.transform(val[config.key], config)}
          </span>
        </div>`).join('')
      return row
    },
    renderChunk (fromPos, howMany) {
      let fragment = document.createDocumentFragment();
      this.scroller.style.height = fromPos * this.rowHeight + 'px'
      fragment.appendChild(this.headerEl)
      fragment.appendChild(this.scroller)
      this.fromPos = fromPos
      this.howMany = howMany

      this.totalHeight = this.rowHeight * this.data.length

      var finalItem = fromPos + howMany;
      if (finalItem > this.data.length) finalItem = this.data.length;

      for (var i = fromPos; i < finalItem; i++) {
        var item = this.generatorFn(this.computedData[i])

        item.classList.add("row");
        item.style.height = this.rowHeight + "px";
        fragment.appendChild(item);
      }
      this.$refs.table.innerHTML = "";
      this.$refs.table.appendChild(fragment);
      this.tableWidth = this.tableWidth || [...this.headerEl.querySelectorAll('header .col')].reduce((accum, el) => accum + el.offsetWidth, 0) + this.header.length + 3
    }
  },
  mounted () {
    window.scroller = this
    this.scroller = this.createScroller()
    this.headerEl = this.createHeader()
    let lastRepaintY;
    let { height } = this.$el.childNodes[0].getBoundingClientRect()
    let screenItemsLen = Math.ceil(height / this.rowHeight);
    // Cache 4 times the number of items that fit in the container viewport
    let cachedItemsLen = screenItemsLen * 2;
    this.renderChunk(0, cachedItemsLen);
    let  maxBuffer = screenItemsLen * this.rowHeight;

    this.$el.childNodes[0].addEventListener('scroll', (ev) => {
      let scrollTop = ev.target.scrollTop
      let first = (scrollTop - this.offsetHeight) / this.rowHeight

      first = first < 0 ? 0 : first

      if (!lastRepaintY || Math.abs(scrollTop - lastRepaintY) > maxBuffer || scrollTop < lastRepaintY) {
        this.renderChunk(Math.floor(first), cachedItemsLen);
        lastRepaintY = scrollTop;
      }
    }, { passive: true })
  }
}

</script>

<style scoped>
/deep/ section {
  display: table;
  width: 100px;
  overflow: auto;
}

/deep/ section > * {
  display: table-row;
  white-space: nowrap;
}

/deep/ section .col {
  display: table-cell;
  vertical-align: middle;
}

/deep/ header .col {
  position: sticky;
  top: 0px;
  background: white;
  text-align: left;
  border-bottom: 1px solid #999;
}

.infinite-table-border {
  border: 1px solid #999;
  border-radius: 4px;
}

.infinite-table {
  overflow: auto;
  border: 1px solid #999;
  border-radius: 4px;
  font-size: 13px;
  display: flex;
}

.scroll-tricker {
  width: .1px;
}

/deep/ .col {
  max-width: 200px;
  padding-left: 5px;
  padding-right: 5px;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}

.no-data {
    position: absolute;
    height: 100%;
    display: flex;
    align-items: center;
    width: 100%;
    justify-content: center;
}

</style>

