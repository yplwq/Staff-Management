<template>
  <div>
    <!-- Content Header (Page header) -->
    <section class="content-header">
      <h1>
        部门管理
        <small>Control panel</small>
      </h1>
      <ol class="breadcrumb">
        <li>
          <a href="#"><i class="fa fa-dashboard"></i> 首页</a>
        </li>
        <li class="active">部门管理</li>
      </ol>
    </section>

    <!-- Main content -->
    <section class="content">
      <div class="row">
        <div class="col-md-12">
          <div class="box">
            <div class="box-header">
              <h3 class="box-title">查看部门</h3>
              <div class="box-tools pull-right">
                  <button class="btn btn-info" title="修改" v-on:click="showModal">添加部门</button>
              </div>
            </div>
            <div class="box-body table-responsive no-padding">
              <div v-if="departments.length == 0">
                <h3 style="text-align:center;">部门为空，请先添加部门</h3>
              </div>
              <table class="table table-bordered" v-else>
                <tbody>
                  <tr>
                    <th>id</th>
                    <th>部门名称</th>
                    <th style="width:20%;">查看</th>
                  </tr>
                  <tr v-for="d in departments">
                    <td>{{d.id}}</td>
                    <td>{{d.name}}</td>
                    <td class="icon">
                      <router-link class="btn btn-success" :to="{name:'users',params:{department:d._id}}">查看员工</router-link>
                      <button class="btn btn-warning" v-on:click="del(d._id)">删除部门</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <!-- /.box-body -->
            <!-- box-footer -->
            <div class="box-footer clearfix" v-if="pageCount>1">
              <ul class="pagination pagination-sm no-margin pull-right">
                <li v-bind:class="{ 'disabled': page == 1}"><a href="javascript:void(0)" v-on:click="page--,getDepartments()">«</a></li>
                <li v-for="p in pages" v-bind:class="{ 'active': page == p}"><a href="javascript:void(0)" v-on:click="page=p,getDepartments()">{{p}}</a></li>
                <li v-bind:class="{ 'disabled': page == pageCount}"><a href="javascript:void(0)" v-on:click="page++,getDepartments()">»</a></li>
              </ul>
            </div>
            <!-- /.box-footer -->
          </div>
        </div>
      </div>
    </section>
    <!-- /.content -->
    <!-- modal -->
    <div class="modal fade" id="add" tabindex="-1" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
                    <h4 class="modal-title">添加部门</h4>
                </div>
                <div class="modal-body">
                  <div class="form-group">
                    <label for="name">部门名称</label>
                    <input type="text" class="form-control" id="name" placeholder="名称" v-model="department.name">
                  </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-default" data-dismiss="modal">取消</button>
                    <button type="button" class="btn btn-primary" v-on:click="addDepartment">添加</button>
                </div>
            </div><!-- /.modal-content -->
        </div><!-- /.modal -->
    </div>
  </div>
</template>

<script>
export default {
  name: "department",
  data: function() {
    return {
      departments: [],
      department: {
        name: ""
      },
      page: 1,
      pageCount: 1,
      pages: []
    };
  },
  mounted() {
    this.getDepartments();
  },
  methods: {
    addDepartment() {
      const self = this;
      self.axios
        .post("/api/admin/department/add", self.department)
        .then(res => {
          self.$toasted.show(res.data.msg, {
            theme: "outline",
            position: "top-center",
            duration: 5000
          });
          if (res.data.code == 0) {
            self.department.name = "";
          } else {
            $("#add").modal("hide");
            self.getDepartments();
          }
        });
    },
    del(id) {
      var self = this;
      self.axios.post("/api/admin/department/delete/" + id).then(({ data }) => {
        self.$toasted.show(data.msg, {
          theme: "outline",
          position: "top-center",
          duration: 3000
        });
        self.getDepartments();
      });
    },
    getDepartments() {
      var self = this;
      self.axios
        .post("/api/admin/department/list", { page: self.page })
        .then(({ data }) => {
          self.departments = data.departments;
          self.page = data.page;
          self.pageCount = data.pageCount;
          self.pages = data.pages;
        });
    },
    showModal() {
      $("#add").modal("show");
    }
  }
};
</script>

<style>
</style>
