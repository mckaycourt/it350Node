<template>
    <div id="app" :class="{ loaded: loaded }">
        <h1 v-if="!user">Login</h1>
        <form method="POST" action="/api/users/login" v-if="!user">
            <p>Username: <input type="text" name="username"></p>
            <p>Password: <input type="password" name="password"></p>
            <p><input type="submit"></p>
        </form>
        <h1 v-if="!user">Create User</h1>
        <form method="POST" action="/api/users" v-if="!user">
            <p>Username: <input type="text" name="username"></p>
            <p>Password: <input type="password" name="password"></p>
            <p><input type="submit"></p>
        </form>
        <div v-else>
            <div id="items">
            <button @click="logout()">Log Out {{user.username}}</button>

            <p v-for="item in items" :key="item.id">{{ item.itemName }}
                <button @click="addToCart(item)">add to cart</button>
            </p>
            </div>
            <div id="Cart">
                <h1>Cart</h1>
                <p v-for="(value, key, index) in cart">{{ value.itemName }}
                    <button @click="removeFromCart(value, key)">remove from cart</button>
                </p>
            </div>

        </div>


        <!-- this content is route based -->
        <div id="content-area">
            <router-view></router-view>
        </div>
    </div>
</template>
<script>
    import axios from 'axios';

    export default {
        data: function () {
            this.getInitData();
            this.getItems();
            this.getCart();
            return {
                loaded: false,
                friends: [],
                user: null,
                items: [],
                cart: []
            }
        },
        methods: {
            getInitData: function () {
                fetch('/api/init', {credentials: 'same-origin'})
                    .then(response => response.json())
                    .then(data => {
                        this.friends = data.friends;
                        this.user = data.user;
                        this.loaded = true;
                    });
            },
            logout: function () {
                window.location = '/api/users/logout';
            },
            getItems: function () {
                fetch('/api/items/items', {credentials: 'same-origin'})
                    .then(response => response.json())
                    .then(data => {
                        this.items = data;
                    });
            },
            getCart: function () {
                fetch('/api/cart/items', {credentials: 'same-origin'})
                    .then(response => response.json())
                    .then(data => {
                        this.cart = data;
                    });
            },
            addToCart: function (item) {
                axios.post('/api/cart/newItem', {itemName: item.itemName, itemInfo: item.itemInfo, quantity: item.quantity})
                    .then(function (res) {
                        console.log(res);
                    })
                    .catch(function (err) {
                        console.log(err);
                    });
                this.cart.push({itemName: item.itemName, itemInfo: item.itemInfo});
            },
            removeFromCart: function (item, itemId) {
                console.log(itemId);
                this.cart.splice(itemId, 1);
                axios.post('/api/cart/removeItem', {itemName: item.itemName, itemInfo: item.itemInfo})
                    .then(function (res) {
                        console.log(res);
                    })
                    .catch(function (err) {
                        console.log(err);
                    });
            }
        }
    }
</script>

<style scoped>
    #app {
        opacity: 0;
        transition: opacity 300ms ease-out;
    }

    #app.loaded {
        opacity: 1;
    }

    #Cart {
        float: right;
    }
    #items{
        float: left;
    }
</style>