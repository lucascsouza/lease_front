<template>
  <div class="container-fluid">
    <h4 class="mt-3">Server List</h4>
    <div class="row mt-4">
      <div class="col-4">
        <label class="form-label">Storage</label>
        <div class="d-flex">
          <input type="range"
                 class="form-range w-75"
                 step="1" min="0" 
                 :max="Object.keys(filterData.storage).length"
                 @change="tranlasteSelectedStorage"
                 v-model="filter.storage">
          <span class="ms-2">{{ storageSelected }}</span>
        </div>
      </div>
      <div class="col-8">
        <label class="form-label w-100">RAM</label>
        <div class="form-check form-check-inline" v-for="value in filterData.ram">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" :value="value" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">{{ value }}GB</label>
        </div>
      </div>
      
    </div>
    <div class="row mt-2">
      <div class="col-4">
        <label class="form-label">Location</label>
        <select class="form-select" v-model="filter.location">
          <option></option>
          <option v-for="(value, index) in filterData.locations" :value="value" :key="index">{{ value }}</option>
        </select>
      </div>
      <div class="col-2">
        <label class="form-label">Disk Type</label>
        <select class="form-select" v-model="filter.disk_type">
          <option></option>
          <option v-for="value in filterData.disk_types" :value="value">{{ value }}</option>
        </select>
      </div>
    </div>
    <div class="row mt-2">
      <div class="col-6">
        <button class="btn btn-primary" type="button" @click="search">Filter</button>
        <button class="btn btn-primary ms-2" type="button" @click="clearFilter">Clear Filter</button>
      </div>
    </div>
    <div class="row mt-3" v-if="compareList.length > 0">
      <h6>Comparison Table</h6>
      <div class="col-12">
        <table class="table table-bordered table-hover w-100">
          <thead>
          <tr>
            <th class="fw-bold">Model</th>
            <th class="fw-bold">RAM</th>
            <th class="fw-bold">HDD</th>
            <th class="fw-bold">Location</th>
            <th class="fw-bold">Price</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(row, index) in compareList" :key="index">
            <td class="text-nowrap">{{ row.name }}</td>
            <td class="text-nowrap">{{ row.ram }}GB{{ row.ram_specification }}</td>
            <td class="text-nowrap">{{ row.storage_description }}</td>
            <td class="text-nowrap">{{ row.location }}</td>
            <td class="text-nowrap">{{ row.currency + row.price }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="row mt-3">
      <div class="col-12">
        <table class="table table-bordered table-hover w-100">
          <thead>
          <tr>
            <th class="fw-bold">Compare</th>
            <th class="fw-bold">Model</th>
            <th class="fw-bold">RAM</th>
            <th class="fw-bold">HDD</th>
            <th class="fw-bold">Location</th>
            <th class="fw-bold">Price</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(row, index) in dataList" :key="index">
            <td class="text-center">
              <input type="checkbox" :value="index" @change="addToComparison">
            </td>
            <td class="text-nowrap">{{ row.name }}</td>
            <td class="text-nowrap">{{ row.ram }}GB{{ row.ram_specification }}</td>
            <td class="text-nowrap">{{ row.storage_description }}</td>
            <td class="text-nowrap">{{ row.location }}</td>
            <td class="text-nowrap">{{ row.currency + row.price }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DataList",
  data() {
    return {
      apiAccessToken: import.meta.env.VITE_ACCESS_API_TOKEN,
      baseURL: import.meta.env.VITE_API_BASE_URL,
      dataList: null,
      filterData: {
        storage: [],
        disk_types: [],
        ram: [],
        locations: []
      },
      compareList: [],
      filter: {
        storage: null, 
        disk_type: null, 
        location: null, 
        ram: []
      },
      storageSelected: null,
      storageMapping: []
    }
  },
  methods: {
    fetchData() {
      fetch(this.baseURL + '/servers', {
        headers: {
          "Authorization": "Bearer " + this.apiAccessToken
        },
      })
          .then(res => res.json())
          .then(res => this.dataList = res)
    },
    fetchFilterData() {
      fetch(this.baseURL + '/filter-data', {
        headers: {
          "Authorization": "Bearer " + this.apiAccessToken
        },
      })
          .then(res => res.json())
          .then(res => {
            this.filterData = res
            this.mapStorage()
          })
    },
    search (e) {
      this.compareList = []
      let body = {
        storage_alias: this.filter.storage ? this.storageSelected : null,
        disk_type: this.filter.disk_type,
        location: this.filter.location,
        ram: this.filter.ram,
      }
      let queryParams = new URLSearchParams(body).toString()

      fetch(this.baseURL + '/servers?' + queryParams, {
        headers: {
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.apiAccessToken
        },
      })
          .then(response => response.json())
          .then(response => {
            console.log(response)
            this.dataList = response
          })
    },
    clearFilter() {
      this.storageSelected = null
      this.filter = {
        storage: null,
        disk_type: null,
        location: null,
        ram: []
      }
      this.search()
    },
    mapStorage () {
      Object.entries(this.filterData.storage).sort((a, b) => {}).forEach((value) => {
        this.storageMapping.push(value[0])
      })
      this.tranlasteSelectedStorage()
    },
    tranlasteSelectedStorage () {
      this.storageSelected = this.storageMapping[this.filter.storage]
    },
    addToComparison(e) {
      if (e.target.checked) {
        this.compareList.push(this.dataList[e.target.value])
      } else {
        let indexToRemove = this.compareList.indexOf(this.dataList[e.target.value])
        this.compareList.splice(indexToRemove, 1)
      }
    }
  },
  created() {
    this.fetchData()
    this.fetchFilterData()
    this.mapStorage()
  }
}
</script>

<style scoped>

</style>