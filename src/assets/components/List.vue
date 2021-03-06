<template>
  <div class="table-container">
    <CircleLoader v-if="loading" />
    <section v-for="(categoryGroup,index) in categorizedList" :key="index">
      <h4 v-if="groupby">{{categoryGroup.category}}</h4>
      <table>
        <thead>
          <tr>
            <slot name="head">
              <th
                v-for="(column,index) in header"
                :key="index"
                :style="{width:column.width+'%'}"
              >{{column.label}}</th>
            </slot>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in categoryGroup.entries" :key="index">
            <slot name="row" :item="item" :editItem="editItem">
              <td v-for="(column, index) in header" :key="index">{{item[column.key]}}</td>
            </slot>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script>
import _ from "lodash";
import CircleLoader from "@/assets/components/CircleLoader";

export default {
  name: "List",
  components: { CircleLoader },
  props: {
    loading: Boolean,
    header: Array,
    groupby: String,
    data: Array
  },
  computed: {
    categorizedList() {
      if (this.groupby) {
        return _(this.data)
          .groupBy(x => x[this.groupby])
          .map((value, key) => ({ category: key, entries: value }))
          .value();
      } else {
        return [{ category: "", entries: this.data }];
      }
    }
  },
  methods: {
    formatHyperlink(val) {
      if (typeof val === "string") {
        if (val.substr(0, 4) === "http") {
          return `<a href="${val}" target="_blank">${val}</a>`;
        }
        if (val.indexOf("@") > 0 && val.indexOf(" ") === -1) {
          return `<a href="mailto:${val}">${val}</a>`;
        }
      }
      return val;
    },
    editItem(index) {
      this.$emit("edit", index);
    }
  }
};
</script>

<style scoped>
.table-container {
  max-width: 960px;
  width: 100%;
  margin: 0 auto;
  margin-top: 30px;
  font-family: Avenir;
  font-size: 0.9em;
}
table {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
  table-layout: fixed;
}

thead {
  color: rgba(0, 0, 0, 0.6);
}

tr {
  border-bottom: 1px solid rgba(0, 0, 0, 0.12);
}

tbody > tr:hover {
  background-color: #eee;
}

td,
th {
  padding: 15px 5px;
  display: table-cell;
  text-align: left;
  vertical-align: middle;
  border-radius: 2px;
}

td {
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}

td.overflow{
  white-space: initial;
}

section {
  margin-bottom: 70px;
}

h4 {
  font-size: 1.5em;
  text-transform: uppercase;
  color: #2196f3;
}

.edit-cell {
  text-align: center;
  cursor: pointer;
}

.edit-cell:hover > .edit-icon {
  transform: scale(1.1, 1.1);
}

.edit-icon {
  max-width: 20px;
  transition: transform 0.3s;
  transform: scale(1, 1);
}
</style>