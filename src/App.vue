<template>
<div>

    <div class="container">
        <div class="row">
            <form @submit.enter="submit" class="row l4 s12 card-panel  col z-depth-1  cv_form  ">               
                <div class="input-field col ">
                    <select v-model="select" id="select">
                        <option value="header" selected>Header</option>
                        <option value="subheader">Subheader</option>
                        <option value="image">Image</option>
                        <option value="text">Text</option>
                    </select>
                    <label for="select">1) Choose type of block</label>
                </div>
                <div class="input-field col s12">
                    <textarea @input="textarea_validation" id="textarea1" v-model="textarea" class="materialize-textarea textarea"></textarea>
                    <label for="textarea1">2) Type text or if it image, put here image url</label>
                    <span>At least 3 symbols. You enter: {{textarea.length}}</span>
                </div>

                <button class="waves-effect waves-light input-field btn col s12" type="submit" :disabled="textarea_validation">Add {{this.select}}</button>
            </form>

            <div class="col  l8 s12 card-panel  teal lighten-2 cv_wrapper">
                <p v-if="content_items.length == 0 ? true : false">No data</p>
                <cv-component :component="component_item" v-for="component_item in content_items" @delete_cv_part="delete_cv_part" :key="component_item.id"></cv-component>
            <span>Your data saves in firebase</span>
            </div>
        </div>
    </div>
    <app-alert :alert="alert" @close_alert="show_alert=!show_alert" v-if="show_alert"></app-alert>

</div>
</template>

<script>
import CvComponent from './components/CvComponent.vue'
import AppAlert from './components/AppAlert.vue'

export default {
    name: 'App',
    data() {
        return {
            textarea: "",
            select: "header",
            show_alert: false,
            errors: {
                submit: false
            },
            content_items: []
        }
    },
    components: {
        CvComponent,
        AppAlert,

    },
    methods: {
        delete_cv_part(id) {
            this.content_items = this.content_items.filter(item => item.id !== id) //удаление элемента из портфолио
            fetch(`https://cvs-info-default-rtdb.firebaseio.com/cvs/${id}.json`, {
                    method: "DELETE",
                    headers: {
                        "Content-Type": 'application/json'
                    },
                    body: JSON.stringify({
                        cv_data: this.content_items
                    })
                })
                .then((response) => {
                    return response.json();
                })
                .then((data) => {
                    console.log(data);
                });

        },
        submit(e) {
            e.preventDefault();
            if (this.select == "image") {
                if (/\.(jpe?g|png|gif|bmp|webp)$/i.test(this.textarea)) {
                    this.content_items.push({
                        id: this.content_items.length + 1,
                        type: this.select,
                        text: this.textarea
                    })
                    fetch("https://cvs-info-default-rtdb.firebaseio.com/cvs.json", {
                            method: "POST",
                            headers: {
                                "Content-Type": 'application/json'
                            },
                            body: JSON.stringify({
                                cv_data: this.content_items
                            })
                        }).then((response) => {
                            return response.json();

                        })
                        .then(() => {
                            fetch('https://cvs-info-default-rtdb.firebaseio.com/cvs.json')
                                .then((response) => {
                                    return response.json();
                                })
                                .then((data) => {

                                    const flatObject = (o) =>
                                        Object.entries(o)
                                        .map(([id, v]) =>
                                            Array.isArray(v.cv_data) ? {
                                                ...v.cv_data.pop(),
                                                id
                                            } :
                                            flatObject(v.cv_data)
                                        )
                                        .flat();

                                    this.content_items = flatObject(data)

                                });
                            // данные которые мы получаем после добавления из в базу

                        });

                    this.show_alert = true;
                    this.alert = {
                        type: "success",
                        header: "Element " + this.select,
                        text: "successfully added",
                    }

                } else {
                    this.show_alert = true;
                    this.alert = {
                        type: "error",
                        header: "Be careful",
                        text: "this is not image url",
                    }
                }
            } else {
                this.show_alert = true;
                this.alert = {
                    type: "success",
                    header: "Element " + this.select,
                    text: "successfully added",
                }
                this.content_items.push({
                    id: this.content_items.length + 1,
                    type: this.select,
                    text: this.textarea
                })
                fetch("https://cvs-info-default-rtdb.firebaseio.com/cvs.json", {
                        method: "POST",
                        headers: {
                            "Content-Type": 'application/json'
                        },
                        body: JSON.stringify({
                            cv_data: this.content_items
                        })
                    }).then((response) => {
                        return response.json();
                    })
                    .then(() => {
                        fetch('https://cvs-info-default-rtdb.firebaseio.com/cvs.json')
                            .then((response) => {
                                return response.json();
                            })
                            .then((data) => {
                                const flatObject = (o) =>
                                    Object.entries(o)
                                    .map(([id, v]) =>
                                        Array.isArray(v.cv_data) ? {
                                            ...v.cv_data.pop(),
                                            id
                                        } :
                                        flatObject(v.cv_data)
                                    )
                                    .flat();
                                this.content_items = flatObject(data)

                            });
                        // данные которые мы получаем после добавления их в базу

                    });

            }
            this.textarea = "";

        },

    },
    computed: {
        textarea_validation() {
            if (this.textarea.length > 3) {
                return this.errors.submit == true;
            } else {
                return this.errors.submit == false;
            }
        }
    },
    created() {
        fetch('https://cvs-info-default-rtdb.firebaseio.com/cvs.json')
            .then((response) => {
                return response.json();
            })
            .then((data) => {
               
                const flatObject = (o) =>
                    Object.entries(o)
                    .map(([id, v]) =>
                        Array.isArray(v.cv_data) ? {
                            ...v.cv_data.pop(),
                            id
                        } :
                        flatObject(v.cv_data)
                    )
                    .flat();
                this.content_items = flatObject(data)
            });
        // данные которые мы получаем после добавления из в базу
    }
}
</script>

<style>
#textarea1 {
    margin-top: 30px;
}

form {
    border: 1px solid #ba68c8;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
.cv_wrapper img{
        max-width: 100%;
}
.cv_form  {
        padding-top: 20px !important;  
}
.delete{
    cursor: pointer;
}
</style>
