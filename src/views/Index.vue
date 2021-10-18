<template>
  <div>
    <div class="csv-input">
      Please input csv file:
      <input type="file" @change="onFileChange" />
      <br />
      <label for="has-header">
        CSV has header
        <input type="checkbox" v-model="header" name="" id="has-header" />
      </label>
      <div id="result"></div>
    </div>

    <div class="result" v-for="(item, i) in node" :key="i">{{ item }}</div>
  </div>
</template>

<script>
import _ from 'lodash';
export default {
  data() {
    return {
      header: false,
      csvData: {},
      node: [],
    };
  },
  methods: {
    onFileChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.csvRead(files[0]);
    },
    csvRead(file) {
      const config = {
        header: false,
        complete: this.onComplete,
      };
      if (this.header) {
        config.header = this.header;
      }
      this.$papa.parse(file, config);
    },
    checkOneColumn(csv) {
      let valid = true;
      if (csv.data[0].length > 1) {
        valid = false;
      }
      return valid;
    },
    onComplete(results) {
      if (this.checkOneColumn(results)) {
        this.csvData = results;
        const flatten = _.flatten(results.data);
        this.csvData = flatten;
        this.extractNode(this.csvData);
      } else {
        alert('file has more than one column');
      }
    },
    extractNode(data) {
      let unique = [];
      data.forEach((elem) => {
        const noDigit = elem.replace(/\d+/g, '');
        const singleChar = noDigit.split('');
        unique.push(singleChar);
        const flatten = _.flatten(unique);
        unique = [...new Set(flatten)];
      });
      this.node = unique;
    },
  },
};
</script>

<style lang="scss" scoped></style>
