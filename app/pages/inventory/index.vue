<template>
    <div>
        <h3> Inventory List</h3>
        <v-card>
            <v-card-text>
                <v-text-field v-model="search" density="compact" label="Search" prepend-inner-icon="mdi-magnify"
                    variant="solo-filled" flat hide-details single-line></v-text-field>
                <v-data-table :headers="headers" :items="items" :tiem-key="name"> </v-data-table>
            </v-card-text>
        </v-card>
    </div>

</template>

<script setup>
const headers = [
    { title: "Name" },
    { title: "Description" },
    { title: "Serial Number" },
    { title: "Quantity" },
    { title: "Unit" },
    { title: "Item Status" },
    { title: "Purchase Date" },



]
</script>