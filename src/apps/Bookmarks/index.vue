<template>
  <div class="app-container">
    <AppHeader
      :title="header.title"
      :new-button="header.newButton"
      :description="header.description"
      @new="newRecord"
      @search="searchRecords"
    />

    <List :header="list.header" :groupby="list.groupby" :data="list.data" @edit="editRecord"/>
    <Modal v-if="modal.show" :title="modal.title" @close="closeModal">
      <p>Modal Data Goes Here</p>
      <p>Debug:</p>
      <pre>{{modal.data}}</pre>
    </Modal>
  </div>
</template>

<script>
import AppHeader from "@/assets/components/AppHeader";
import List from "@/assets/components/List";
import Modal from "@/assets/components/Modal";
import { baseUrl } from "@/assets/libs/baseUrl";

export default {
  name: "Contacts",
  components: { AppHeader, List, Modal },
  data() {
    return {
      header: {
        title: "Bookmarks",
        newButton: "New Bookmark",
        description:
          "A collection of commonly used websites sorted by category. Click on a row to see more. This is a staff curated list, so please update any records you notice are incorrect/out of date or add new contacts that you notice are missing."
      },
      list: {
        header: [
          { label: "Name", width: 25, key: "name" },
          { label: "URL", width: 50, key: "url" },
          { label: "Notes", width: 25, key: "notes" }
        ],
        groupby: "category",
        data: []
      },
      modal: { show: false, title: "", data:null }
    };
  },
  created() {
    this.getList();
  },
  methods: {
    getList() {
      fetch(`${baseUrl}/bookmarks/get-list`)
        .then(res => res.json())
        .then(data => {
          this.list.data = data;
        })
        .catch(err => {
          console.log("Error fetching bookmarks list");
        });
    },
    editRecord(id) {
      console.log(id);
      this.modal.title = "Edit Record";
      this.modal.show = true;
      this.modal.data = this.list.data.filter(n=>n.id===id);
    },
    saveRecord(id) {},
    deleteRecord(id) {},
    newRecord() {
      this.modal.title = "New Record";
      this.modal.data = null;
      this.modal.show = true;
    },
    closeModal() {
      this.modal.show = false;
    },
    searchRecords(query) {}
  }
};
</script>