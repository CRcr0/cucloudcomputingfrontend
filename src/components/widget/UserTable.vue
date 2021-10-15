<template>
	<el-table
	    :data="users"
	    border
	    style="width:95.2%;margin:0px auto;" size="medium" >
	    <el-table-column
	      prop="id"
	      label="id"
	      width="100">
	    </el-table-column>
	    <el-table-column
	      prop="username"
	      label="Username">
	    </el-table-column>
	    <el-table-column
	      prop="mobile"
	      label="phone number">
	    </el-table-column>
	    <el-table-column
	      prop="email"
	      label="email">
	    </el-table-column>
	 </el-table>
</template>

<script>
export default {
  name: 'UserTable',
  props:['users'],
  data () {
    return {
    }
  }
}
</script>
