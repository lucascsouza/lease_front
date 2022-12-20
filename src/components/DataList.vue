<template>
  <div class="container-fluid w-100">
    <div class="row">
      <div class="col-6">
        <label for="customRange1" class="form-label">Storage</label>
        <div class="d-flex">
          <input type="range" class="form-range w-75" id="customRange1" step="1" min="1" max="12" @change="tranlasteSelectedStorage" v-model="filter.storage">
          <span class="ms-2">{{ storageSelected }}</span>
        </div>
      </div>
      <div class="col-6">
        <label for="customRange1" class="form-label">Disk Type</label>
        <select class="form-select" v-model="filter.disk_type">
          <option></option>
          <option value="SAS">SAS</option>
          <option value="SATA2">SATA</option>
          <option value="SSD">SSD</option>
        </select>
      </div>
    </div>
    <div class="row mt-2" style="font-size: 12px">
      <label for="customRange1" class="form-label">RAM</label>
      <div class="col-12">
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="2" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">2GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="4" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">4GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="8" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">8GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="12" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">12GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="16" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">16GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="24" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">24GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="32" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">32GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="48" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">48GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="64" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">64GB</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="checkbox" id="inlineCheckbox1" value="96" v-model="filter.ram">
          <label class="form-check-label" for="inlineCheckbox1">96GB</label>
        </div>
      </div>
    </div>
    <div class="row mt-2">
      <div class="col-6">
        <label for="customRange1" class="form-label">Location</label>
        <select class="form-select" v-model="filter.location">
          <option></option>
          <option value="AmsterdamAMS-01">AmsterdamAMS-01</option>
          <option value="sata">DallasDAL-10</option>
          <option value="ssd">Washington D.C.WDC-01</option>
          <option value="ssd">SingaporeSIN-11</option>
        </select>
      </div>
      <div class="col-6 mt-4">
        <button class="btn btn-primary" @click="search">Filter</button>
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
      dataList: null,
      compareList: [],
      filter: {
        storage: null, 
        disk_type: null, 
        location: null, 
        ram: []
      },
      storageSelected: 1,
      storageMapping: {
        1: '',
        2: '240GB',
        3: '480GB',
        4: '1TB',
        5: '2TB',
        6: '3TB',
        7: '4TB',
        8: '8TB',
        9: '12TB',
        10: '24TB',
        11: '48TB',
        12: '72TB',
      }
    }
  },
  methods: {
    getData() {
      fetch('http://docker.localhost/api/test', {
        headers: {
          "Authorization": "Bearer " + this.apiAccessToken
        },
      })
          .then(res => res.json())
          .then(res => this.dataList = res);
    },
    tranlasteSelectedStorage (e) {
      this.storageSelected = this.storageMapping[e.target.value];
    },
    search (e) {
      this.compareList = []
      let body = {
        storage: this.storageSelected,
        disk_type: this.filter.disk_type,
        location: this.filter.location,
        ram: this.filter.ram,
      }
      let queryParams = new URLSearchParams(body).toString()
      console.log(queryParams);

      fetch('http://docker.localhost/api/test?'+queryParams, {
        headers: {
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.apiAccessToken
        },
      })
          .then(response => response.json())
          .then(response => {
            this.dataList = response
          })     
    },
    addToComparison(e) {
      if (e.target.checked) {
        this.compareList.push(this.dataList[e.target.value]);
      } else {
        let indexToRemove = this.compareList.indexOf(this.dataList[e.target.value]);
        this.compareList.splice(indexToRemove, 1);
      }
      console.log(e, this.dataList[e.target.value]);
    }
  },
  created() {
    this.getData();
    this.storageSelected = null;
  }
}
</script>

<style scoped>

</style>