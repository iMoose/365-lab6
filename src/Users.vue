<template>
  <div>
    <div v-if="errorFlag" style="color: red;">
      {{ error }}
    </div>

    <div v-if="$route.params.userId">
      <div id="user">
        <router-link :to="{ name: 'users' }">Back to users</router-link>

        <br><br>

        <table>
          <tr>
            <td>User ID</td>
            <td>Username</td>
          </tr>
          <tr>
            <td>{{ $route.params.userId }}</td>
            <td>{{ getSingleUser($route.params.userId).username }}</td>
          </tr>
        </table>

        <button type="button" class="btn btn-primary" data-toggle="modal" data-target="editUserModal">Edit</button>
        <button type="button" class="btn btn-danger" data-toggle="modal" data-target="#deleteUserModal">Delete</button>
      </div>

      <div class="modal fade" id="deleteUserModal" tabindex="-1" role="dialog" aria-labelledby="deleteUserModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
          <div class="model-content">
            <div class="modal-header">
              <h5 class="modal-title" id="deleteUserModalLabel">Delete User</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              Are you sure you want to delete this user?
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-seconday" data-dismiss="modal">
                Close
              </button>
              <button type="button" class="btn btn-primary" data-dismiss="modal" v-on:click="deleteUser($route.params.userId)">
                Delete User
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="modal fade" id="deleteUserModal" tabindex="-1" role="dialog" aria-labelledby="deleteUserModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
          <div class="model-content">
            <div class="modal-header">
              <h5 class="modal-title" id="deleteUserModalLabel">Delete User</h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              Are you sure you want to delete this user?
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-seconday" data-dismiss="modal">
                Close
              </button>
              <button type="button" class="btn btn-primary" data-dismiss="modal" v-on:click="deleteUser($route.params.userId)">
                Edit User
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>

    <div v-else>
      <div id="users">
        <table>
          <tr v-for="user in users">
            <td>{{ user.username }}</td>
            <td><router-link :to="{ name: 'user', params: { userId: user.user_id }}">View</router-link></td>
          </tr>
        </table>
      </div>


    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        error: "",
        errorFlag: false,
        users: []
      }
    },
    mounted: function (){
      this.getUsers();
    },
    methods: {
      getUsers: function() {
          this.$http.get('http://localhost:8080/api/users')
            .then(function(response) {
              this.users = response.data;
            }, function(error) {
                this.error = error;
                this.errorFlag = true;
            });
      },
      getSingleUser: function(id) {
        let numUsers = this.users.length;
        for (var i = 0; i < numUsers; i++) {
          if (this.users[i].user_id == id) {
            return this.users[i];
          }
        }
      },
      deleteUser: function(user_id) {
        this.$http.delete('http://localhost:8080/api/users/' + user_id)
          .then(function(response) {
              let numUsers = this.users.length;
              for (var i = 0; i < numUsers; i++) {
                if (user_id == this.users[i].user_id) {
                  this.users.splice(i, 1)
                }
              }

              this.$router.push('/users');
          },
          function(error) {
            this.error = error;
            this.errorFlag = true;
          })
      }
    }
  }
</script>
