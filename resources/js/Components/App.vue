<template>
    <div class="container">
        <h1>{{ title }}</h1>
        <div class="alert alert-warning"
             v-if="alert">
            {{ alert }}
        </div>
        <div class="form-group">
            <label class="text-primary" for="token" v-model="status">{{ status }}</label>
            <input type="text"
                   id="token"
                   v-model="token"
                   class="form-control"
                   placeholder="Token">
        </div>
        <div class="row">
            <div class="col-12 col-md-4">
                <div id="register" class="card">
                    <div class="card-header">Register</div>
                    <div class="card-body">
                        <form @submit="register"
                              action="/api/register"
                              method="post">
                            <div class="form-group">
                                <label for="inputName">Name</label>
                                <input type="text"
                                       class="form-control"
                                       id="inputName"
                                       v-model="inputName"
                                       aria-describedby="nameHelp"
                                       placeholder="Enter name"
                                       required>
                            </div>
                            <div class="form-group">
                                <label for="inputEmail">Email</label>
                                <input type="email"
                                       class="form-control"
                                       id="inputEmail"
                                       v-model="inputEmail"
                                       aria-describedby="emailHelp"
                                       placeholder="Enter email"
                                       required>
                            </div>
                            <div class="form-group">
                                <label for="inputPassword">Password</label>
                                <input type="password"
                                       class="form-control"
                                       id="inputPassword"
                                       v-model="inputPassword"
                                       placeholder="Enter password"
                                       required>
                            </div>
                            <div class="form-group">
                                <label for="inputDeviceName">DeviceName</label>
                                <input type="text"
                                       class="form-control"
                                       id="inputDeviceName"
                                       v-model="inputDeviceName"
                                       aria-describedby="deviceNameHelp"
                                       placeholder="Enter device name"
                                       required>
                            </div>
                            <button type="submit"
                                    class="btn btn-primary">Submit
                            </button>
                        </form>
                    </div>
                </div>
            </div>
            <div class="col-12 col-md-4">
                <div id="login" class="card">
                    <div class="card-header">Login</div>
                    <div class="card-body">
                        <form @submit="login"
                              action="/api/login"
                              method="post">
                            <div class="form-group">
                                <label for="inputEmailLogin">Email</label>
                                <input type="email"
                                       class="form-control"
                                       id="inputEmailLogin"
                                       v-model="inputEmail"
                                       aria-describedby="emailHelp"
                                       placeholder="Enter email"
                                       required>
                            </div>
                            <div class="form-group">
                                <label for="inputPasswordLogin">Password</label>
                                <input type="password"
                                       class="form-control"
                                       id="inputPasswordLogin"
                                       v-model="inputPassword"
                                       placeholder="Enter password"
                                       required>
                            </div>
                            <div class="form-group">
                                <label for="inputDeviceNameLogin">DeviceName</label>
                                <input type="text"
                                       class="form-control"
                                       id="inputDeviceNameLogin"
                                       v-model="inputDeviceName"
                                       aria-describedby="deviceNameHelp"
                                       placeholder="Enter device name"
                                       required>
                            </div>
                            <button type="submit"
                                    class="btn btn-primary">Submit
                            </button>
                        </form>
                    </div>
                </div>
            </div>
            <div class="col-12 col-md-4">
                <div id="user"
                     class="card">
                    <div class="card-header">User</div>
                    <div class="card-body">
                        <div v-if="userData">
                            <p><strong>name:</strong> {{ userData.name }}</p>
                            <p><strong>email:</strong> {{ userData.email }}</p>
                            <p><strong>email_verified_at:</strong> {{ userData.email_verified_at }}</p>
                            <p><strong>created_at:</strong> {{ userData.created_at }}</p>
                        </div>
                        <p v-else>...</p>
                    </div>
                    <div class="card-footer">
                        <div class="btn btn-success"
                             @click="getData(token)">
                            Get User Data
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            title: String,
        },
        data() {
            return {
                token: "",
                status: "Guest",
                inputName: "",
                inputEmail: "",
                inputPassword: "",
                inputDeviceName: "",
                alert: "",
                data: {},
                userData: {},
            };
        },
        mounted() {
        },

        methods: {
            register: function (e) {
                const requestOptions = {
                    method: "POST",
                    headers: {"Content-Type": "application/json"},
                    body: JSON.stringify({
                        name: this.inputName,
                        email: this.inputEmail,
                        password: this.inputPassword,
                        device_name: this.inputDeviceName,
                    })
                };
                fetch("/api/register", requestOptions)
                    .then(response => response.json())
                    .then(data => (this.processData(data)));
                e.preventDefault();
            },
            login: function (e) {
                const requestOptions = {
                    method: "POST",
                    headers: {"Content-Type": "application/json"},
                    body: JSON.stringify({
                        email: this.inputEmail,
                        password: this.inputPassword,
                        device_name: this.inputDeviceName,
                    })
                };
                fetch("/api/login", requestOptions)
                    .then(response => response.json())
                    .then(data => (this.processData(data)));
                e.preventDefault();
            },
            getData(token) {
                const requestOptions = {
                    method: "get",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": "Bearer " + token,
                    }
                };
                fetch("/api/user", requestOptions)
                    .then(response => response.json())
                    .then(data => (this.processData(data)));
            },
            processData(data) {
                this.$set(this.data, 'response', data);
                this.alert = JSON.stringify(this.data);
                if (this.data.response.token) {
                    this.token = this.data.response.token;
                    this.status = "Logged";
                }
                if (this.data.response.user) {
                    this.userData = Object.assign({}, this.userData, {
                        'name': this.data.response.user.name,
                        'email': this.data.response.user.email,
                        'email_verified_at': this.data.response.user.email_verified_at,
                        'created_at': this.data.response.user.created_at,
                    });
                }
            },
        }
    }
</script>
