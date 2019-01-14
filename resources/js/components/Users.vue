<!--suppress ALL -->
<template>
    <div class="container">
        <div class="row mt-4">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h3 class="card-title">User Table</h3>

                        <div class="card-tools">
                            <!--<div class="input-group input-group-sm" style="width: 150px;">-->
                            <!--<input type="text" name="table_search" class="form-control float-right" placeholder="Search">-->

                            <!--<div class="input-group-append">-->
                            <!--<button type="submit" class="btn btn-default"><i class="fa fa-search"></i></button>-->
                            <!--</div>-->
                            <!--</div>-->
                            <button class="btn btn-success " data-toggle="modal" data-target="#addNew">Add New <i
                                    class="fas fa-user-plus"></i></button>
                        </div>
                    </div>
                    <!-- /.card-header -->
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover">
                            <tbody>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>Email</th>
                                <th>Type</th>
                                <th>Register At</th>
                                <th>Modify</th>
                            </tr>
                            <tr v-for="user in users" :key="user.id">
                                <td>{{user.id}}</td>
                                <td>{{user.name}}</td>
                                <td>{{user.email }}</td>
                                <td>{{user.type | upText }}</td>
                                <td>{{user.created_at | myDate}}</td>

                                <td><a href=""><i class="fas fa-user-edit blue"></i></a>
                                    | <a href="#" @click="deleteUser(user.id)"><i class="fas fa-user-times red"></i></a>
                                </td>
                            </tr>

                            </tbody>
                        </table>
                    </div>
                    <!-- /.card-body -->
                </div>
                <!-- /.card -->
            </div>
        </div>
        <div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="addNewLabel"
             aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="addNewLabel">Add New</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <form @submit.prevent="createUser" @keydown="form.onKeydown($event)">
                        <div class="modal-body">

                            <div class="form-group">
                                <input v-model="form.name" type="text" name="name" placeholder="Name"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                <has-error :form="form" field="name"></has-error>
                            </div>

                            <div class="form-group">

                                <input v-model="form.email" type="email" name="email"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                <has-error :form="form" field="email"></has-error>
                            </div>

                            <div class="form-group">
                                <textarea v-model="form.bio" type="textarea" name="bio"
                                          class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }">
                                    <has-error :form="form" field="bio"></has-error></textarea>
                            </div>
                            <div class="form-group">

                                <select v-model="form.type" type="type" name="type"
                                        class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="">Select role User</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">Standar User</option>
                                    <option value="author">Author</option>
                                    <has-error :form="form" field="type"></has-error>
                                </select>
                            </div>
                            <div class="form-group">

                                <input v-model="form.password" type="password" name="password"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                <has-error :form="form" field="password"></has-error>
                            </div>


                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                            <button type="submit" class="btn btn-primary">Create</button>
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
                users: {},
                form: new Form({
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''

                }),
            }
        },
        methods: {
            deleteUser(id) {
                Swal({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    //send request to the server
                    //vform paquete esto lo hace ese apquete ver doc en github
                    if(result.value){
                        this.form.delete('api/user/' + id).then(() => {

                            Swal(
                                'Deleted!',
                                'Your file has been deleted.',
                                'success'
                            )
                            Fire.$emit('AfterCreate');
                        }).catch(() => {
                            Swal("Failed!", "There was something  wronge", "warning");
                        });

                    }

                })
            },
            loadUsers() {
                axios.get('api/user').then(({data}) => (this.users = data.data));
            },
            createUser() {
                this.$Progress.start()
                this.form.post('api/user').then(() => {
                    Fire.$emit('AfterCreate');
                    toast({
                        type: 'success',
                        title: 'Signed in successfully'
                    })
                    $("#addNew").modal('hide');
                    this.$Progress.finish();
                }).catch(() => {

                })

            }
        },
        created() {
            this.loadUsers();
            // setInterval(()=>this.loadUsers(), 3000)
            Fire.$on('AfterCreate', () => {
                this.loadUsers();
                // this.loadUsers();

            })
        }
    }
</script>
