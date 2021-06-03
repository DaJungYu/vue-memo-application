<template>
    <div class="memo-app">
        <memo-form v-on:addMemo="addMemo"/>
        <ul class="memo-list">
        <memo v-for="memo in memos" :key="memo.id" :memo="memo" @deleteMemo="deleteMemo"/>
        </ul>
    </div>
</template>

<script>
import MemoForm from './MemoForm';
import Memo from './Memo';

export default {
    name: 'MemoApp',
    components: {
        MemoForm,
        Memo
    },
    methods:{
        addMemo(payload){
            this.memos.push(payload);
            this.storeMemo();
        },
        deleteMemo(id) { //자식 컴포넌트에서 인자로 전달해주는 id에 해당하는 메모 데이터의 인덱스 찾기
            const targetIndex = this.memos.findIndex(v => v.id === id); // 찾은 인덱스 번호에 해당하는 메모데이터 삭제
            this.memos.splice(targetIndex,1);//삭제된 후 데이터를 다시 로컬 스토리지에 마찬가지로 저장.
            this.storeMemo();
        },
        storeMemo() {
            const memosToString =JSON.stringify(this.memos);
            localStorage.setItem('memos', memosToString);
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