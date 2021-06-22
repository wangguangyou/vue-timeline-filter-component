<template>
  <div id="app">
    <HelloWorld ref="vtf" style="width:300px" :list="list">
      <template #slider="scoped">
        <el-slider @input="change" style="margin-top:-19px" v-model="scoped.value" range :max="scoped.data.length-1">
        </el-slider>
      </template>
    </HelloWorld>
  </div>
</template>

<script>
  import HelloWorld from './components/vue-timeline-filter-component'

  export default {
    name: 'App',
    components: {
      HelloWorld
    },
    data() {
      return {
        list: [
          { label: '2000', value: 131 },
          { label: '2001', value: 213 },
          { label: '2002', value: 31 },
          { label: '2003', value: 2412 },
          { label: '2004', value: 561 },
          { label: '2005', value: 522 },
          { label: '2006', value: 211 },
          { label: '2007', value: 241 },
          { label: '2008', value: 421 },
          { label: '2009', value: 2432 },
          { label: '2010', value: 15 },
          { label: '2011', value: 232 },
          { label: '2012', value: 521 },
          { label: '2013', value: 2532 },
          { label: '2014', value: 2351 },
          { label: '2015', value: 522 },
          { label: '2016', value: 12153 },
          { label: '2017', value: 12532 },
          { label: '2018', value: 13521 },
          { label: '2019', value: 5232 },
          { label: '2020', value: 11253 },
          { label: '2021', value: 5232 }]
      }
    },
    methods:{
      change(val){
        this.$refs.vtf.value = val
        console.log(val)
      }
    }
  }
</script>
