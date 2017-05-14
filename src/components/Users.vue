<template lang="html" id='users'>
  <div>
    <div class="form" v-if="onCreate || onEdit">
      <h3 v-if="onCreate">New User</h3>     
      <h3 v-if="onEdit">Edit User</h3>       
      <div class="row">
        <div class="col-md-6">
          <label for="name">Name</label>
          <span style="color: red;">{{ formErrors.name }}</span>          
          <input type="text" name="name" v-model="user.name" class="form-control" />          
          <label for="username">Username</label>
          <span style="color: red;">{{ formErrors.username }}</span>          
          <input type="text" name="username" v-model="user.username" class="form-control" />                    
          <label for="role">Role</label>
          <span style="color: red;">{{ formErrors.role }}</span>
          <select class="form-control" name="role" v-model="user.role">
            <option v-for="rol in roles" v-bind:value="rol">{{ rol }}</option>            
          </select>               
        </div>
        <div class="col-md-6">
          <label for="email">Email</label>
          <span style="color: red;">{{ formErrors.email }}</span> 
          <input type="email" name="email" v-model="user.email" class="form-control" />                    
          <label for="password">Password</label>
          <span style="color: red;">{{ formErrors.password }}</span>
          <input type="password" name="password" v-model="user.password" class="form-control" />          
        </div>
      </div>
      <div class="row">
        <div class="col-md-2 offset-md-5">  
        <a href="#" @click="createUser" v-if="onCreate" class="btn btn-success">Create</a>
        <a href="#" @click="updateUser" v-if="onEdit" class="btn btn-success">Edit</a>        
        </div>
      </div>
    </div>      
    <div class="list" v-if="!onCreate && !onEdit">
      <div class="row">
        <div class="col-md-11">
          <h3>Users</h3>        
        </div>
        <div class="col-md-1">
          <a href="#" @click="fetchAll">Refresh</a>
        </div>  
      </div>
      <table class="table table-sm">
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Username</th>
          <th>Email</th>
          <th>Role</th>
          <th>Actions</th>      
        </tr>
        <tr v-for="(user, index) in Users">
          <td>{{ user.id }}</td>
          <td>{{ user.name }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.role }}</td>
          <td>
            <a href="#" @click="editUser(index)">Edit</a>
            <a href="#" @click="removeUser(user.id)">Delete</a>
          </td>
        </tr>
      </table>        
    </div>    
    <a href="#" class="btn btn-primary" @click="onCreate = true">Add user</a>    
  </div>     
</template> 
<script>
export default {
  name: 'Users',
  data () {
    return {
      onCreate: false,
      onEdit: false,
      user: { username: '', name: '', role: 'customer', email: '', password: '' },
      Users: [],
      formErrors: {},
      roles: ['customer', 'support', 'admin']
    }
  },
  methods: {
    fetchAll: function () {
      this.$http.get('users.json')
        .then(data => {
          this.Users = data.body
        })
    },
    createUser: function () {
      this.$http.post('users.json', this.user)
      .then(response => {
        this.fetchAll()
        this.onCreate = false
        this.user = { username: '', name: '', role: '', email: '', password: '' }
      }, response => {
        this.formErrors = response.body
      })
    },
    editUser: function (index) {
      this.onEdit = true
      this.user = this.Users[index]
    },
    updateUser: function () {
      this.$http.patch('users/' + this.user.id + '.json', this.user)
      .then(response => {
        this.onEdit = false
        this.user = { username: '', name: '', role: '', email: '', password: '' }
      }, response => {
        this.formErrors = response.body
      })
    },
    removeUser: function (id) {
      this.$http.delete('users/' + id + '.json')
      .then(() => this.fetchAll())
    }
  },
  mounted: function () {
    this.fetchAll()
  }
}
</script>
<style lang="css">
</style>
