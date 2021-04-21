<template>
  <div>
    <info-base :employee="employee"/>
    <el-row :gutter="20">
      <el-form v-model="crime" label-position="left" label-width="80px">
        <el-col :span="16">
          <el-card header="违纪信息">
            <el-col :span="12">
              <el-form-item label="违纪">
                <el-input
                  v-model="crime.detail"
                  type="textarea"
                  placeholder="请输入违纪的详细信息"
                  :rows="7">

                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="违纪时间">
                <el-date-picker
                  v-model="crime.critime"
                  type="date"
                  placeholder="选择违纪时间"
                  :picker-options="pickerOptions">
                </el-date-picker>
              </el-form-item>
              <el-form-item label="违纪程度">
                <el-select v-model="crime.rank">
                  <el-option
                    v-for="(rank, index) in crime_ranks"
                    :key="index"
                    :value="index"
                    :label="rank"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="提交">
                <el-button @click="handleSubmit">提交</el-button>
              </el-form-item>
            </el-col>
          </el-card>
        </el-col>
        <el-col :span="8">
          <el-card header="温馨提示">
            由于不同企业的判断违纪程度的方式不同，请酌情选择
          </el-card>
        </el-col>
      </el-form>
    </el-row>
  </div>
</template>

<script>
import {searchInfobyId} from "@/api/info";
import {crime_ranks} from "@/utils/info-format";
import {inputCrime} from '@/api/career'
import InfoBase from "@/views/components/info/info-base";

export default {
  name: "crime",
  components: {InfoBase},
  data(){
    return {
      employee: {},
      crime: {
      },
      crime_ranks,
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now();
        },
        shortcuts: [{
          text: '今天',
          onClick(picker) {
            picker.$emit('pick', new Date());
          }
        }, {
          text: '昨天',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24);
            picker.$emit('pick', date);
          }
        }, {
          text: '一周前',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', date);
          }
        }]
      }
    }
  },
  methods: {
    init() {
      this.crime.empid = this.$route.params.id
      this.getEmployee()
    },
    getEmployee(){
      searchInfobyId(this.$route.params.id).then(res=>{
        if (res.data.code === 100) {
          this.employee = res.data.data
        }
      })
    },
    handleSubmit(){
      inputCrime(this.crime).then(res => {
        if (res.data.code === 100) {
          this.$message.success(res.data.msg)
          this.$router.push('/employee/crime')
        }else {
          this.$message.error(res.data.msg)
        }
      })
    }
  },
  mounted() {
    this.init()
  }
}
</script>

<style scoped>

</style>
