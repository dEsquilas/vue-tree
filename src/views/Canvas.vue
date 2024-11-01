<template>
    <section class="min-h-screen min-w-screen bg-gray-800 p-4">
        <Node v-for="node in nodes" :key="node.id" :node="node" />
    </section>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import Node from '../components/Node.vue'

const nodes = ref([
    {
        id: 1,
        label: "Node name 1",
        description: "Node description 1",
        childs: [
            {
                id: 2,
                label: "Node name 2",
                childs: [
                    {
                        id: 3,
                        label: "Node name 3",
                        childs: []
                    }
                ]
            }
        ]
    },
    {
        id: 4,
        label: "Node name 4",
        description: "Node description 4",
        childs: []
    },
    {
        id: 5,
        label: "Node name 5",
        description: "Node description 5",
        childs: [
            {
                id: 22,
                label: "Node name 22",
                childs: [
                    {
                        id: 3,
                        label: "Node name 3a",
                        childs: []
                    }
                ]
            },
            {
                id: 23,
                label: "Node name 23",
                childs: [
                    {
                        id: 3,
                        label: "Node name 3f",
                        childs: []
                    }
                ]
            }
        ]
    },
    {
        id: 6,
        label: "Node name 6",
        description: "Node description 6",
        childs: []
    }
])

</script>
