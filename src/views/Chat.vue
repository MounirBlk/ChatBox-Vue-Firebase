<template>
    <div class="container chat">
        <h2 class="text-primary text-center">Real-Time Chat Box</h2>
        <h5 class="text-secondary text-center">Vuejs and Firebase</h5>
        <div class="card-body">
            <p class="text-secondary nomessages" v-if="messages.length == 0">
                [Aucun messages]
            </p>
            <div class="messages" v-chat-scroll="{always:false, smooth: true}">
                <div v-for="(message,index) in messages" :key="index">
                    <span class="text-info">[{{ message.name }}]</span>
                    <span>{{ message.message }}</span>
                    <span class="text-secondary time">{{ message.timestamp }}</span>
                </div>
            </div>
            <div class="card-action">
                <CreateMessage :name="name" />
            </div>
        </div>
    </div>
</template>

<script>
// @ is an alias to /src
import CreateMessage from '@/components/CreateMessage.vue';
import fb from '@/firebase/init';
import moment from 'moment';

export default {
    name: 'Chat',
    props:['name'],
    components: {
        CreateMessage
    },
    data(){
        return{
            messages: []
        }
    },
    created() {
        let ref = fb.collection('messages').orderBy('timestamp');
        ref.onSnapshot(snapshot => {
            snapshot.docChanges().forEach(change => {
                if(change.type == 'added'){
                    let doc = change.doc;
                    this.messages.push({
                        id: doc.id,
                        name: doc.data(),
                        message: doc.data().message,
                        timestamp: moment(doc.data().timestamp).format('D/M/yyyy H:mm:ss')
                    });
                }
            });
        });
    },
    methods: {},
}
</script>
<style>
.chat h2{
    font-size: 2.6em;
    margin-bottom: 0px;
}
.chat h5{
    margin-top: 0px;
    margin-bottom: 40px;
}
.chat span{
    font-size: 1.2em;
}
.chat .time{
    display: block;
    font-size: 0.7em;
}
.messages{
    max-height: 300px;
    overflow: auto;
}
</style>