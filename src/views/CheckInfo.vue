<template>
  <div>
    <el-breadcrumb separator-icon="ArrowRight" style="margin: 16px">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>卫生管理</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card style="margin: 15px; min-height: calc(100vh - 111px)">
      <div>
        <!--    功能区-->
        <div style="margin: 10px 0;">
          <!--    搜索区-->
          <div style="margin: 10px 0;">
            <el-input
              v-model="search"
              clearable
              placeholder="请输入宿舍号"
              prefix-icon="Search"
              style="width: 20%;20px;"
            />
            <el-date-picker
            style="margin-left:20px;"
              v-model="date"
              type="datetimerange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            >
            </el-date-picker>
            <el-button
              icon="Search"
              style="margin-left: 5px"
              type="primary"
              @click="load"
            ></el-button>
            <el-button
              icon="refresh-left"
              style="margin-left: 10px"
              type="default"
              @click="reset"
            ></el-button>
            <div style="float: right">
              <el-tooltip content="添加" placement="top">
                <el-button
                  icon="plus"
                  v-if="role != 'stu'"
                  style="width: 50px"
                  type="primary"
                  @click="add"
                ></el-button>
              </el-tooltip>
            </div>
          </div>
          
        </div>
        <!--    表格-->
        <el-table
          v-loading="loading"
          :data="tableData"
          border
          max-height="705"
          style="width: 100%"
        >
          <el-table-column label="#" type="index" />
          <el-table-column label="宿舍号" prop="roomid" sortable />
          <el-table-column label="检查结果" prop="grade" />
          <el-table-column label="说明原因" prop="reason" />
          <el-table-column label="时间" prop="date" />
          <el-table-column label="操作人" prop="operatorName" />
          <!--      操作栏-->
          <el-table-column v-if="role != 'stu'" label="操作" width="130px">
            <template #default="scope">
              <el-button
                icon="Edit"
                type="primary"
                @click="handleEdit(scope.row)"
              ></el-button>
              <el-popconfirm
                title="确认删除？"
                @confirm="handleDelete(scope.row.id)"
              >
                <template #reference>
                  <el-button icon="Delete" type="danger"></el-button>
                </template>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <!--分页-->
        <div style="margin: 10px 0">
          <el-pagination
            v-model:currentPage="currentPage"
            :page-size="pageSize"
            :page-sizes="[10, 20]"
            :total="total"
            layout="total, sizes, prev, pager, next, jumper"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
          >
          </el-pagination>
        </div>
        <!--      弹窗-->
        <div>
          <el-dialog
            v-model="dialogVisible"
            title="操作"
            width="30%"
            @close="cancel"
          >
            <el-form
              ref="form"
              :model="form"
              :rules="rules"
              label-width="120px"
            >
              <el-form-item label="宿舍号" prop="roomid">
                <el-input
                  v-model="form.roomid"
                  placeholder="输入宿舍号"
                  clearable
                  style="width: 80%"
                ></el-input>
              </el-form-item>
              <el-form-item label="检查结果" prop="grade">
                <el-select
                  v-model="form.grade"
                  style="width: 80%"
                  placeholder="请选择检查结果"
                >
                  <el-option label="及格" value="及格"></el-option>
                  <el-option label="不及格" value="不及格"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="说明原因" prop="reason">
                <el-input
                  v-model="form.reason"
                  placeholder="输入原因"
                  type="textarea"
                  :rows="4"
                  clearable
                  style="width: 80%"
                ></el-input>
              </el-form-item>
            </el-form>
            <template #footer>
              <span class="dialog-footer">
                <el-button @click="cancel">取 消</el-button>
                <el-button type="primary" @click="save">确 定</el-button>
              </span>
            </template>
          </el-dialog>
        </div>
      </div>
    </el-card>
  </div>
</template>
<script src="@/assets/js/CheckInfo.js"></script>