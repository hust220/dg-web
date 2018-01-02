<template>
<div class="index">
  <el-row>
    <el-col :span="20" :offset="2">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>DistDB</span>
        </div>
        <el-form ref="form" :model="form" label-width="250px">
          <el-form-item label="Please select a type for search">
            <el-select v-model="form.type" placeholder="Select">
              <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="Please input the 2D structure" v-show="form.type==='secondary_structure'">
            <el-input v-model="form.ss"></el-input>
          </el-form-item>

          <el-form-item label="Please upload the 3D structure" v-show="form.type==='tertiary_structure'">
            <input type="file" ref="ts_file">
          </el-form-item>

          <el-form-item label="Please input all the base interactions" v-show="form.type==='base_interactions'">
            <el-input type="textarea" rows=10 v-model="form.base_interactions"></el-input>
          </el-form-item>

          <el-form-item label="">
            <a href="#" @click.prevent="show_options^=true">{{show_options?'Hide':'Show'}} options</a>
          </el-form-item>

          <el-form-item label="Maximum number of matched motifs" v-show="show_options">
            <el-input v-model="form.max_num"></el-input>
          </el-form-item>

          <el-form-item label="Search range" v-show="show_options">
            <el-select v-model="form.range_type" placeholder="Select">
              <el-option v-for="item in range_options" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="Please input PDB IDs" v-show="show_options && form.range_type==='pdb_ids'">
            <el-input v-model="form.pdb_ids" placeholder="1Y26, 1A64"></el-input>
          </el-form-item>

          <el-form-item label="Please upload the 3D structure" v-show="show_options && form.range_type==='ts'">
            <input type="file" ref="range_ts_file">
          </el-form-item>

          <el-form-item label="Atom name" v-show="show_options && form.type==='tertiary_structure'">
            <el-input v-model="form.atom"></el-input>
          </el-form-item>

          <el-form-item label="Cutoff" v-show="show_options && form.type==='tertiary_structure'">
            <el-input v-model="form.cutoff"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button @click="onSubmit" type="primary">Submit</el-button>
            <el-button>Cancel</el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </el-col>
  </el-row>
</div>
</template>

<script>
import axios from 'axios'

axios.defaults.headers.post['Content-Type'] = 'multipart/form-data'

export default {
  name: 'Index',
  data () {
    return {
      show_options: false,
      range_options: [{
        label: 'PDB database',
        value: 'all'
      }, {
        label: 'PDB IDs',
        value: 'pdb_ids'
      }, {
        label: 'Upload tertiary structures',
        value: 'ts'
      }],
      options: [{
        label: 'Secondary Structure',
        value: 'secondary_structure'
      }, {
        label: 'Tertiary Structure',
        value: 'tertiary_structure'
      }, {
        label: 'Base Interactions',
        value: 'base_interactions'
      }],
      form: {
        pdb_ids: '',
        range_type: 'all',
        atom: 'C1\'',
        max_num: 5,
        ss: '(....)',
        type: 'secondary_structure',
        cutoff: 0.8
      }
    }
  },
  methods: {
    onSubmit () {
      var formData = new FormData()
      for (var i in this.form) {
        formData.append(i, this.form[i])
      }
      formData.append('rec_file', this.$refs.rec_file.files[0])
      formData.append('lig_file', this.$refs.lig_file.files[0])
      formData.append('ref_file', this.$refs.ref_file.files[0])
      axios.post('submit', formData).then(response => {
        console.log(response)
      }).catch(() => {
        console.log('post failed')
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.box-card {
  padding: 0px 150px;
}
</style>
