<template>
    <div>
        <h1>Jobs List</h1>
        <table class="table table-bordered">
            <thead>
                <th>Job Title</th>
                <th>Job Description</th>
                <th>Vacant Slots</th>
                <th>Actions</th>
            </thead>
            <tbody>
                <tr v-for="(job, index) in jobsList" :key="job.id">
                    <td>{{job.job_title}}</td>
                    <td>{{job.job_desc}}</td>
                    <td>{{job.job_slots}}</td>
                    <td>
                        <button class="btn btn-secondary btn-sm" @click="editModal(job)"><i class="fas fa-edit"></i></button>
                        <button class="btn btn-danger btn-sm" @click="del(job.id, index)"><i class="fas fa-trash"></i></button>
                    </td>
                </tr>
                <tr>
                    <td>
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Enter Job Title" v-model="form.job_title">
                        </div>
                    </td>
                    <td>
                        <div class="form-floating">
                            <textarea class="form-control text-area" placeholder="Enter Job Description" id="floatingTextarea" v-model="form.job_desc"></textarea>
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="number" class="form-control" placeholder="Enter Vacant Slots" v-model="form.job_slots">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <button class="btn-primary btn-sm" @click="submit"><i class="fas fa-plus"></i></button>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>

        <!-- Modal -->
        <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="editModalLabel">Modal title</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span area-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <div class="form-group mb-3">
                            <label>Job Title</label>
                            <input type="text" class="form-control" placeholder="Enter Job Title" v-model="formEdit.job_title">
                        </div>
                        <div class="form-group mb-3">
                            <label>Job Description</label>
                            <input type="text" class="form-control" placeholder="Enter Job Title" v-model="formEdit.job_desc">
                        </div>
                        <div class="form-group mb-3">
                            <label>Vacant Slots</label>
                            <input type="text" class="form-control" placeholder="Enter Job Title" v-model="formEdit.job_slots">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button type="button" class="btn btn-primary" @click="update">Save changes</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props:['jobs_list'],
    data(){
        return {
            jobsList: this.jobs_list,
            form: {
                job_title: '',
                job_desc: '',
                job_slots: 0
            },
            formEdit: {
                job_title: '',
                job_desc: '',
                job_slots: 0
            },
            jobID: null,

        }
    },
    methods: {
        submit(){
            const vm = this;
            axios.post('/job_posting', this.form)
            .then(function (response){
                vm.jobsList.push(response.data.data)
                vm.form.job_title = '';
                vm.form.job_desc = '';
                vm.form.job_slots = 0;
            })
            .catch(function (error){
                console.log(error)
            });
        },
        del(id, index){
            const vm = this;
            axios.delete('/job_posting/' + id)
            .then(function(response){
                vm.jobsList.splice(index, 1)
            })
            .catch(function(error){
                alert(error)
            })
        },
        editModal(job){
            const vm = this;
            $('#editModal').modal('show');
            vm.formEdit.job_title = job.job_title;
            vm.formEdit.job_desc = job.job_desc;
            vm.formEdit.job_slots = job.job_slots;
            vm.jobID = job.id;
        },
        update(){
            const vm = this;
            axios.put(`/job_posting/${vm.jobID}`, this.formEdit)
            .then(function(){
                $('#editModal').modal('hide');
                alert('successfully updated')
            })
            .catch(function(error){
                alert(error)
            })
        }
    }
}
</script>



<style scoped>
.text-area{
    height: calc(1.6em + 0.75rem + 2px);
}
</style>