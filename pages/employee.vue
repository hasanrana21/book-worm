<template>
  <v-container>
    <!-- SHOW EMPLOYEE LISTS -->
    <ui-table
      v-if="!this.openCreateForm && !this.openUpdateForm"
      :headers="this.headers"
    >
      <template #header>
        <span class="headline">Employee Lists</span>
        <span @click="openForm()">
          <ui-button label="Add New"></ui-button>
        </span>
      </template>
      <tr v-for="(item, key) in data" :key="key">
        <td class="title font-weight-regular py-4">{{ item.name }}</td>
        <td class="title font-weight-regular py-4">{{ item.title }}</td>
        <td class="title font-weight-regular py-4">{{ item.email }}</td>
        <td class="title font-weight-regular py-4">{{ item.phone }}</td>
        <td class="title font-weight-regular py-4">
          <span
            class="mdi mdi-square-edit-outline headline mr-6"
            style="cursor: pointer"
            @click="handleUpdate(item)"
          ></span>
          <span
            class="mdi mdi-trash-can-outline headline"
            style="cursor: pointer"
            @click="employeeDelete(item)"
          ></span>
        </td>
      </tr>
    </ui-table>
    <div
      v-if="!this.data.length && !this.openCreateForm"
      class="text-center pt-10"
    >
      <p class="headline">No Data Found</p>
    </div>

    <!-- EMPLOYEE CREATE FORM -->
    <employee-create-form
      v-if="this.openCreateForm"
      @cancel="handleClose"
      @submit="handleSubmit"
    ></employee-create-form>

    <!-- EMPLOYEE UPDATE FORM -->
    <employee-update-form
      v-if="this.openUpdateForm"
      @close="handleClose"
      @submit="handleSubmit"
    ></employee-update-form>
  </v-container>
</template>
<script>
import UiTable from '@/components/ui/table/index.vue'
import UiButton from '@/components/ui/button/index.vue'
import EmployeeCreateForm from '@/components/local/employee/CreateForm.vue'
import EmployeeUpdateForm from '@/components/local/employee/UpdateForm.vue'
export default {
  name: 'employee',
  components: { UiTable, UiButton, EmployeeCreateForm, EmployeeUpdateForm },
  data() {
    return {
      openCreateForm: false,
      openUpdateForm: false,
      data: [],
      headers: [
        {
          label: 'Name',
        },
        {
          label: 'Title',
        },
        {
          label: 'Email',
        },
        {
          label: 'Phone',
        },
        {
          label: 'Action',
        },
      ],
    }
  },
  methods: {
    openForm() {
      this.openCreateForm = true
    },
    handleClose() {
      this.$store.commit('Employee/clearEmployee')
      this.openCreateForm = false
      this.openUpdateForm = false
    },
    fetchEmployee() {
      let lists = this.$store.state.Employee.employeeLists
      this.data = lists
    },
    handleUpdate(item) {
      this.$store.commit('Employee/setEmployee', item)
      this.openUpdateForm = true
    },
    handleSubmit() {
      this.fetchEmployee()
      this.openCreateForm = false
      this.openUpdateForm = false
    },
    employeeDelete(item) {
      this.$swal({
        title: 'Are you sure for delete the employee?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!',
      }).then((result) => {
        if (result.isConfirmed) {
          this.$store.commit('Employee/deleteEmployee', item)
          this.$swal('Deleted!', 'Your file has been deleted.', 'success')
          this.fetchEmployee()
        }
      })
    },
  },
  beforeMount() {
    this.fetchEmployee()
  },
}
</script>
