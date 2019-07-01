<template>
  <div>
    <div>
      <b-form @submit="onSubmit" @reset="onReset" v-if="show">
        <b-form-group id="input-group-1" label="Name *" label-for="input-1" class="col-md-6">
          <b-form-input
            id="input-1"
            v-model="form.name"
            required
            placeholder="Unique name for represent the domain"
          ></b-form-input>
        </b-form-group>

        <b-form-group id="input-group-2" label="Protocol *" label-for="input-2" class="col-md-6">
          <b-form-select id="input-2" v-model="form.protocols" :options="protocols" required></b-form-select>
        </b-form-group>

        <b-form-group id="input-group-3" label="Host *" label-for="input-3" class="col-md-6">
          <b-form-input id="input-3" v-model="form.host" required placeholder="Host Name"></b-form-input>
        </b-form-group>

        <b-form-group id="input-group-4" label="Port *" label-for="input-4" class="col-md-6">
          <b-form-input id="input-4" v-model="form.port" required placeholder="Port"></b-form-input>
        </b-form-group>

        <b-button type="submit" variant="primary">Submit</b-button>
        <b-button type="reset" variant="danger">Reset</b-button>
      </b-form>
    </div>
    <!--<b-table striped hover :items="items"></b-table>-->
    <table class="table table-hover">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">
            <b>Name</b>
          </th>
          <th scope="col">
            <b>Protocol</b>
          </th>
          <th scope="col">
            <b>Host</b>
          </th>
          <th scope="col">
            <b>Port</b>
          </th>
          <th scope="col">
            <b>Actions</b>
          </th>
        </tr>
      </thead>
      <tbody v-if="domains != null">
        <tr v-for="domain in domains" :key="domain._id">
          <th scope="row">1</th>
          <td>{{domain.form.name}}</td>
          <td>{{domain.form.protocol}}</td>
          <td>{{domain.form.url}}</td>
          <td>{{domain.form.port}}</td>
          <td>
            <a
              href="#Modal1"
              data-toggle="modal"
              data-target="#viewModel"
              @click="setSelectedDomain(domain)"
            >
              <i class="fas fa-search iconspace"></i>
            </a>
            <a
              href="#Modal1"
              data-toggle="modal"
              data-target="#editModel"
              @click="setSelectedDomain(domain)"
            >
              <i class="fas fa-pencil-alt iconspace"></i>
            </a>
            <a
              href="#Modal1"
              data-toggle="modal"
              data-target="#deleteModel"
              @click="setDomainId(domain.id)"
            >
              <i class="fas fa-trash-alt iconspace"></i>
            </a>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { all } from "q";
export default {
  data() {
    return {
      domains : [], 
      form: {
        name: "",
        protocols: null,
        host: "",
        port: ""
      },
      protocols: [{ text: "Select One", value: null }, "HTTPS", "HTTP"],
      show: true
    };
  },
  methods: {
    onSubmit(evt) {
      this.createDomain();
    },
    onReset(evt) {
      evt.preventDefault();
      // Reset our form values
      this.form.email = "";
      this.form.name = "";
      this.form.food = null;
      this.form.checked = [];
      // Trick to reset/clear native browser form validation state
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    },

    createDomain() {
      this.errors = [];

      if (!this.form.name) {
        this.errors.push("Domain name required.");
      }

      if (!this.form.protocols) {
        this.errors.push("Protocol required.");
      }

      if (!this.form.host) {
        this.errors.push("Host required.");
      }

      if (this.errors.length != 0) {
        return false;
      }
      this.axios
        .post("http://localhost:8080/api/v1/domains", {
          name: this.form.name,
          protocol: this.form.protocols,
          url: "www." + this.form.host,
          port: this.form.port
        })
        .then(() => {
          debugger;
          alert('1234444');
          this.getDomains();
          this.message = "New domain added sucessfully!";
          this.showMessage = true;
        })
        .catch(e => {
          console.error(e);
          this.message = e.response.data;
          this.showMessage = true;
        });
    },

    updateDomain(id) {
      this.errors = [];

      if (!this.selectedDomain.name) {
        this.updateErrors.push("Domain name required.");
      }

      if (!this.selectedDomain.protocol) {
        this.updateErrors.push("Protocol required.");
      }

      if (!this.selectedDomain.host) {
        this.updateErrors.push("Host required.");
      }

      if (this.updateErrors.length != 0) {
        return false;
      }
      this.axios
        .put("http://localhost:8080/api/v1/domains/" + id, {
          name: this.selectedDomain.name,
          protocol: this.selectedDomain.protocol,
          url: this.selectedDomain.host,
          port: this.uport
        })
        .then(() => {
          this.getDomains();
          this.message = "New domain added sucessfully!";
          this.showMessage = true;
        })
        .catch(e => {
          console.error(e);
          this.message = e.response.data;
          this.showMessage = true;
        });
    },

    getDomains() {
      alert('get domain');
      debugger;
      this.axios
        .get("http://localhost:8080/api/v1/domains")
        .then(response => {
          debugger;
          this.domains = response.data;
        })
        .catch(error => console.log(error));
    },

    deleteDomain(id) {
      this.axios
        .delete("http://localhost:8080/api/v1/domains/" + id)
        .then(() => {
          this.getDomains();
        })
        .catch(e => {
          console.error(e);
          this.message = e.response.data;
          this.showMessage = true;
        });
    },

    setDomainId(id) {
      this.domainId = id;
    },

    setSelectedDomain(domain) {
      this.selectedDomain = domain;
    }
  }
};
</script>
