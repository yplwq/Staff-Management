<template>
  <div>
    <!-- Content Header (Page header) -->
    <section class="content-header">
      <h1>
        部门调换申请审核
        <small>Control panel</small>
      </h1>
      <ol class="breadcrumb">
        <li>
          <a href="#">
            <i class="fa fa-dashboard"></i> 首页</a>
        </li>
        <li class="active">部门调换审核</li>
      </ol>
    </section>

    <!-- Main content -->
    <section class="content">
      <div class="row">
        <div class="col-md-12">
          <div class="box">
            <div class="box-header">
              <h3 class="box-title">员工部门调换审核</h3>
            </div>
            <div class="box-body table-responsive no-padding">
              <div v-if="users.length == 0">
                <h3 style="text-align:center;">没有待审核员工</h3>
              </div>
              <table class="table table-bordered" v-else>
                <tbody>
                  <tr>
                    <th style="width: 59px">工牌号</th>
                    <th>姓名</th>
                    <th>原部门</th>
                    <th>申请部门</th>
                    <th style="width: 20%">操作</th>
                  </tr>
                  <tr v-for="user in users">
                    <td style="text-align:center;">{{user.workId}}</td>
                    <td>{{user.name}}</td>
                    <td>
                      <span v-if="user.department.default">{{user.department.default.name}}</span>
                      <span v-else>无</span>
                    </td>
                    <td>
                      <span v-if="user.department.new">{{user.department.new.name}}</span>
                      <span v-else>无</span>
                    </td>
                    <td>
                      <button class="btn btn-success" v-on:click="verify(user.id,true,user.department.new._id)">通过</button>
                      <button class="btn btn-warning" v-on:click="verify(user.id,false)">不通过</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <!-- /.box-body -->
            <!-- box-footer -->
            <div class="box-footer clearfix" v-if="pageCount>1">
              <ul class="pagination pagination-sm no-margin pull-right">
                <li v-bind:class="{ 'disabled': page == 1}"><a href="javascript:void(0)" v-on:click="page--,getVerigies()">«</a></li>
                <li v-for="p in pages" v-bind:class="{ 'active': page == p}"><a href="javascript:void(0)" v-on:click="page=p,getVerigies()">{{p}}</a></li>
                <li v-bind:class="{ 'disabled': page == pageCount}"><a href="javascript:void(0)" v-on:click="page++,getVerigies()">»</a></li>
              </ul>
            </div>
            <!-- /.box-footer -->
          </div>
        </div>
      </div>
    </section>
    <!-- /.content -->
  </div>
</template>

<script>
export default {
  name: "verify",
  data: function() {
    return {
      users: [],
      page: 1,
      pageCount: 1,
      pages: []
    };
  },
  methods: {
    getVerigies() {
      const self = this;
      self.axios.post("/api/admin/verify/department",{page:self.page}).then(function({ data }) {
        self.users = data.users;
        self.page = data.page;
        self.pageCount = data.pageCount;
        self.pages = data.pages;
      });
    },
    verify(id, status, newid) {
      const self = this;
      self.axios
        .post("/api/admin/verify/department/" + id, {
          status: status,
          new: newid
        })
        .then(function({ data }) {
          self.$toasted.show(data.msg, {
            theme: "outline",
            position: "top-center",
            duration: 3000
          });
          self.getVerigies();
        });
    }
  },
  mounted() {
    const self = this;
    self.getVerigies();
  }
};
</script>

<style>
</style>
