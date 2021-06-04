<template>
    <div class="memo-app">
        <memo-form v-on:addMemo="addMemo"/>
        <ul class="memo-list">
        <memo v-for="memo in memos" :key="memo.id" :memo="memo" 
        @deleteMemo="deleteMemo" @updateMemo="updateMemo"/>
        </ul>
    </div>
</template>

<script>
import axios from 'axios';
import MemoForm from './MemoForm';
import Memo from './Memo';

const memoAPIcore =axios.create({
    baseURL: 'http://localhost:8000/api/memos'
});

export default {
    name: 'MemoApp',
    components: {
        MemoForm,
        Memo
    },
    methods:{
        addMemo(payload){
            //this.memos.push(payload);
            //this.storeMemo();
            memoAPIcore.post('/',payload)
            .then(res => {
                this.memos.push(res,data)
            });
        },
        deleteMemo(id) { //자식 컴포넌트에서 인자로 전달해주는 id에 해당하는 메모 데이터의 인덱스 찾기
            const targetIndex = this.memos.findIndex(v => v.id === id); // 찾은 인덱스 번호에 해당하는 메모데이터 삭제
            memoAPIcore.delete(`/${id}`)
                .then(()=> {
                this.memos.splice(targetIndex,1);
            });
            //this.memos.splice(targetIndex,1);//삭제된 후 데이터를 다시 로컬 스토리지에 마찬가지로 저장.
            //this.storeMemo();
        },
        storeMemo() {
            const memosToString =JSON.stringify(this.memos);
            localStorage.setItem('memos', memosToString);
        },
        updateMemo() {
            const {id,content}=payload;
            const targetIndex=this.memos.findIndex(v=>v.id===id);
            const targetMemo=this.memos[targetIndex];

            memoAPIcore.put(`/${id}`,{content})
                .then(()=> {
                    this.memos.splice(targetIndex,1,{...targetMemo,content});
                });
            //this.memos.splice(targetIndex,1,{...targetMemo,content});
            //this.storeMemo();
        },
        created() {
            memoAPIcore.get('/')
            .then(res => {
                this.memos = res.data;
            });
        }
    },
    data() {
        return {
            memos: [],
        };
    },
    created () {
        this.memos = localStorage.memos ? JSON.parse(localStorage.memos) : [];
    },
}
</script>

<style scoped>
.memo-list {
    padding: 20px 0;
    margin: 0;
}
</style>