<template>
  <div class="about">
    <h5 class="mb-5 mt-5">Mis contactos</h5>

    <div>
      <div class="mb-3">
        <div class="input-group">
          <span class="input-group-text" id="name-search-text"><i class="bi bi-search"></i> Buscar por nombre
          </span>
          <input type="search" class="form-control" id="name-search"
            @search="this.toSearch = $event.target.value">
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-body">
        <form @submit.prevent="save()">
          <table class="table table-striped">
            <thead>
              <tr>
                <th colspan="2">
                  <input type="text" placeholder="Nombre" class="form-control" id="name" v-model="newContact.name">
                </th>
                <th>
                  <input type="email" placeholder="Correo electrónico" class="form-control" id="url" v-model="newContact.email">
                </th>
                <th>
                  <input type="text" placeholder="Dirección" class="form-control" id="url" v-model="newContact.address">
                </th>
                <th>
                  <input type="text" placeholder="Teléfono" class="form-control" id="url" v-model="newContact.phone">
                </th>
                <th>
                  <input type="text" placeholder="País" class="form-control" id="url" v-model="newContact.country">
                </th>
                <th>
                  <input type="text" placeholder="Ciudad" class="form-control" id="url" v-model="newContact.city">
                </th>
                <th>
                  <button type="submit" class="btn btn-primary">Agregar</button>
                </th>
              </tr>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Nombre</th>
                <th scope="col">Correo electrónico</th>
                <th scope="col">Dirección</th>
                <th scope="col">Teléfono</th>
                <th scope="col">País</th>
                <th scope="col">Ciudad</th>
                <th></th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="(contact, index) in getList()" :key="index">
                <th scope="row">{{ 1 + index }}</th>
                <td>{{ contact.name }}</td>
                <td>{{ contact.email }}</td>
                <td>{{ contact.address }}</td>
                <td>{{ contact.phone }}</td>
                <td>{{ contact.country }}</td>
                <td>{{ contact.city }}</td>
                <td>
                  <button type="button" class="btn btn-info btn-sm" @click="edit(index)"><i
                      class="bi bi-pen"></i></button>

                  <button type="button" class="btn btn-danger btn-sm" @click="remove(index)"><i
                      class="bi bi-trash3"></i></button>
                </td>
              </tr>
            </tbody>
          </table>
        </form>
      </div>
    </div>

    <!-- Modal -->
    <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="editModalLabel">Editar</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <form @submit.prevent="saveEdit()">
            <div class="modal-body" v-if="itemSelected">
              <div class="mb-3">
                <label for="updateName" class="form-label">Nombre</label>
                <input type="text" v-model="itemSelected.name" class="form-control" id="updateName">
              </div>
              <div class="mb-3">
                <label for="updateEmail" class="form-label">Correo electrónico</label>
                <input type="email" v-model="itemSelected.email" class="form-control" id="updateEmail">
              </div>
              <div class="mb-3">
                <label for="updateAddress" class="form-label">Dirección</label>
                <input type="text" v-model="itemSelected.address" class="form-control" id="updateAddress">
              </div>
              <div class="mb-3">
                <label for="updatePhone" class="form-label">Teléfono</label>
                <input type="text" v-model="itemSelected.phone" class="form-control" id="updatePhone">
              </div>
              <div class="mb-3">
                <label for="updateCountry" class="form-label">País</label>
                <input type="text" v-model="itemSelected.country" class="form-control" id="updateCountry">
              </div>
              <div class="mb-3">
                <label for="updateCity" class="form-label">Ciudad</label>
                <input type="text" v-model="itemSelected.city" class="form-control" id="updateCity">
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
              <button type="submit" class="btn btn-primary">Guardar cambios</button>
            </div>
          </form>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      newContact: {
        name: '',
        email: '',
        address: '',
        phone: '',
        country: '',
        city: ''
      },
      contactsArray: [
        {
          id: 1,
          name: "Alice Johnson",
          email: "alice.johnson@example.com",
          address: "123 Maple Street",
          phone: "123-456-7890",
          country: "USA",
          city: "New York"
        },
        {
          id: 2,
          name: "Bob Smith",
          email: "bob.smith@example.com",
          address: "456 Oak Avenue",
          phone: "987-654-3210",
          country: "Canada",
          city: "Toronto"
        },
        {
          id: 3,
          name: "Carol White",
          email: "carol.white@example.com",
          address: "789 Pine Road",
          phone: "555-123-4567",
          country: "UK",
          city: "London"
        },
        {
          id: 4,
          name: "David Brown",
          email: "david.brown@example.com",
          address: "321 Elm Street",
          phone: "444-555-6666",
          country: "Australia",
          city: "Sydney"
        },
        {
          id: 5,
          name: "Emily Davis",
          email: "emily.davis@example.com",
          address: "654 Spruce Lane",
          phone: "333-444-5555",
          country: "USA",
          city: "Los Angeles"
        }
      ],
      itemSelected: null,
      indexSelected: null,
      typeFilter: '',
      toFilter: '',
      toSearch: ''
    }
  },
  methods: {
    save() {
      // Validar que los campos no estén vacíos
      if (this.newContact.name && this.newContact.email && this.newContact.address && this.newContact.phone && this.newContact.country && this.newContact.city) {
        // Agregar un nuevo objeto al array
        this.contactsArray.push({ ...this.newContact });
        // Limpiar los campos del formulario
        this.newContact.name = '';
        this.newContact.email = '';
        this.newContact.address = '';
        this.newContact.phone = '';
        this.newContact.country = '';
        this.newContact.city = '';
      } else {
        alert("Por favor, completa todos los campos.");
      }
    },
    remove(index) {
      if (confirm("¿Está seguro de eliminar este ítem?")) {
        this.contactsArray.splice(index, 1);
      }
    },
    edit(index) {
      this.itemSelected = { ...this.contactsArray[index] };
      this.indexSelected = index;
      const editModal = new bootstrap.Modal(document.getElementById('editModal'));
      editModal.show();
    },
    saveEdit() {
      this.contactsArray[this.indexSelected] = { ...this.itemSelected };

      const modalElement = document.getElementById('editModal');
      const modalInstance = bootstrap.Modal.getInstance(modalElement) || new bootstrap.Modal(modalElement);
      modalInstance.hide();

      this.itemSelected = null;
      this.indexSelected = null;
    },
    getList() {
      let result = this.contactsArray.filter((item) => {
        if (this.toSearch) {
          return item.name.includes(this.toSearch);
        }
        return true;
      });
      return result;
    }

  }
}
</script>

<style>
.btn {
  margin-right: 3px;
}

.card {
  overflow-x: auto;
}
</style>