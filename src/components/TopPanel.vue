<template>
    <div class="top-panel panel">
        <div v-if="dataLoading" class="loading">
            <div></div>
        </div>
        <table v-else class="data-wrapper">
            <thead>
                <tr>
                    <th>Os. číslo</th>
                    <th>Jméno</th>
                    <th>Docházka (h)</th>
                    <th>Norma (h)</th>
                    <th>Výkon (h)</th>
                    <th>Prostoje (h)</th>
                    <th>Produktivita (%)</th>
                    <th>Odčipováno (%)</th>
                    <th>Suma neshod (Kč)</th>
                    <th>Saldo docházky (h)</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="row,index in data" :key="index">
                    <td>{{row.OsCislo}}</td>
                    <td>{{row.Jmeno}}</td>
                    <td>{{row.Dochazka}}</td>
                    <td>{{row.Norma}}</td>
                    <td>{{row.Vykon}}</td>
                    <td>{{row.Prostoje}}</td>
                    <td>{{getProduktivita(row)}}</td>
                    <td>{{getOdcipovano(row)}}</td>
                    <td>{{row.NeshodyKc}}</td>
                    <td>{{row.SaldoDoch}}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    data() {
        return {
            data: null,
            dataLoading: true,
            direction: 'down'
        }
    },
    async mounted() {
        this.data = await this.getData()
        this.dataLoading = false
        setInterval( async () => {
            this.data = await this.getData()
        }, 3600000)
        setInterval( () => {
            this.scrollTable()
        }, 100)
    },
    methods: {
        getProduktivita(row) {
            return Math.round(row.Norma / (row.Dochazka - row.Prostoje) * 100)
        },
        getOdcipovano() {
            return 0
        },
        async getData() {
            return new Promise( async (resolve, reject) => {
                try {
                    const response = await axios.post(
                        'http://192.168.100.3:8080/sendPost2/PXQ72SBErpZST9nbB98EZMRRhAFvpC',
                        {
                            'id': 'PXQ72SBErpZST9nbB98EZMRRhAFvpC',
                            'typ': 0,
                            'head': 'typUdalosti=40',
                            'items': []
                        },
                        {
                            'Content-Type': 'application/json',
                            'Accept': 'application/json',
                            'charset': 'utf-8', 
                        },
                    )
                    resolve(response.data.payload)
                } catch(e) {
                    reject(e) 
                }
            })
        },
        scrollTable() {
            let table = document.querySelector('.top-panel')
            if (this.direction == 'down') {
                if (table.scrollTop < table.scrollTopMax) {
                    table.scrollBy({
                        top: 2,
                        left: 0,
                        behavior: 'smooth'
                    })
                } else {
                    this.direction = 'up'
                }
            } else {
                if (table.scrollTop > 0) {
                    table.scrollBy({
                        top: -2,
                        left: 0,
                        behavior: 'smooth'
                    })
                } else {
                    this.direction = 'down'
                }
            }
        }
    },
}
</script>

<style lang="scss" scoped>
thead {
    position: sticky;
    top: 0;
    background-color: #FFF;
}
</style>