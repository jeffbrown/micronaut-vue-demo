<template>
    <div class="people">
        <h1>Person Manager Demo</h1>
        <form @submit.prevent="validate() ? addPerson() : null">
            <div class="form-group row">
                <label class="col-sm-2 col-form-label" for="person-firstName">First Name</label>
                <input class="form-control" id="person-firstName" placeholder="Enter First Name" type="text"
                       v-model="newPerson.firstName">
            </div>
            <div class="form-group row">
                <label class="col-sm-2 col-form-label" for="person-lastName">Last Name</label>
                <input class="form-control" id="person-lastName" placeholder="Enter Last Name" type="text"
                       v-model="newPerson.lastName">
            </div>

            <button class="btn btn-primary" type="submit" :disabled="!validate()">Create Person</button>
        </form>

        <person-list class="person-list" :people="people" @deletePerson="deletePerson"></person-list>
    </div>
</template>

<script>
    import PersonList from './PersonList'

    export default {
        name: 'PeopleView',
        data() {
            return {
                people: [],
                newPerson: {
                    firstName: '',
                    lastName: ''
                }
            }
        },
        methods: {
            validate() {
                return (this.newPerson.firstName !== '' && this.newPerson.lastName !== '')
            },
            async loadPeople() {
                const response = await fetch(`${process.env.VUE_APP_SERVER_URL}/people`)
                this.people = await response.json()
            },
            async deletePerson(event) {
                await fetch(`${process.env.VUE_APP_SERVER_URL}/people/${event.id}`, {
                    method: 'DELETE'
                })
                this.loadPeople()
            },
            async addPerson() {
                await fetch(`${process.env.VUE_APP_SERVER_URL}/people`,
                    {
                        method: 'POST',
                        headers: {'Content-Type': 'application/json'},
                        body: JSON.stringify(this.newPerson)
                    })
                this.newPerson.firstName = ''
                this.newPerson.lastName = ''
                this.loadPeople()
            }
        },
        components: {
            personList: PersonList
        },
        created() {
            this.loadPeople()
        }
    }
</script>

<style scoped>
    .people {
        width: 75%;
        margin: 0 auto;
    }
    .person-list {
        margin-top: 50px;
    }
</style>