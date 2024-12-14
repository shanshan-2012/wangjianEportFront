<template>
  <el-container style="height: 100vh;">
    <!-- Header -->
    <el-header style="background-color: #409EFF; color: white; text-align: center;">
      <h2>Excel File Upload Application</h2>
    </el-header>

    <el-container>
      <!-- Left Side Menu -->
      <el-aside width="200px" style="background-color: #f2f2f2;">
        <el-menu default-active="1">
          <el-menu-item index="1">Home</el-menu-item>
          <el-menu-item index="2">Upload Excel</el-menu-item>
        </el-menu>
      </el-aside>

      <!-- Main Section -->
      <el-main>
        <!-- File Upload Form -->
        <el-form ref="fileUploadForm" :model="fileUploadForm" label-width="200px" style="margin-bottom: 20px;">
          <el-form-item label="Select Excel File" prop="file">
            <el-upload
              class="upload-demo"
              ref="fileUpload"
              :http-request="uploadFile"
              :auto-upload="false"
              :file-list="fileList"
              accept=".xls,.xlsx"
            >
              <el-button slot="trigger" type="primary">Choose Excel File</el-button>
              <el-button @click="submitFile" type="success" >Upload</el-button>
              <div slot="tip" class="el-upload__tip">Only Excel files (.xls, .xlsx) are allowed.</div>
            </el-upload>
          </el-form-item>

          <el-form-item>
            <p v-if="uploadProgress > 0">Upload Progress: {{ uploadProgress }}%</p>
          </el-form-item>
        </el-form>

        <!-- Table Section to Display Uploaded Files -->
        <el-table :data="uploadedFiles" style="width: 100%; margin-top: 20px;">
          <el-table-column prop="fileName" label="File Name" width="200"></el-table-column>
          <el-table-column prop="status" label="Status"></el-table-column>
        </el-table>
      </el-main>
    </el-container>

    <!-- Footer -->
    <el-footer style="background-color: #f2f2f2; text-align: center;">
      &copy; 2024 My Application
    </el-footer>
  </el-container>
</template>
<script>
import axios from "axios";

export default {
  name: "FileUploadForm",
  data() {
    return {
      fileUploadForm: {}, // Placeholder for file upload form model
      fileList: [], // Array to store selected files
      uploadProgress: 0, // Progress percentage
      uploadedFiles: [], // Array to store uploaded file details
    };
  },
  methods: {
    // Method to handle Excel file upload
    async uploadFile({ file }) {
      const formData = new FormData();
      formData.append("file", file);
      console.log("start to upload file");
      try {
        // Axios POST request for file upload
        const response = await axios.post("http://52.77.11.70:8092/proformalInvoice/upload", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
          onUploadProgress: (progressEvent) => {
            this.uploadProgress = Math.round(
              (progressEvent.loaded / progressEvent.total) * 100
            );
          },
        });
        console.log("Upload response:", response);

        // Update the uploaded files list
        this.uploadedFiles.push({
          fileName: file.name,
          status: "Uploaded successfully",
        });

        this.$message({
          message: "Excel file uploaded successfully!",
          type: "success",
        });
        console.log("Upload response:", response);
      } catch (error) {
        console.error("File upload error:", error);
        this.$message.error("Excel file upload failed!");
      }
    },

    // Trigger file upload manually
    submitFile() {
      this.$refs.fileUpload.submit();
    },
  },
};
</script>
<style scoped>
/* General Layout */
.el-header {
  height: 60px;
  line-height: 60px;
}

.el-aside {
  padding: 20px;
}

.el-footer {
  height: 40px;
  line-height: 40px;
}

.el-main {
  padding: 20px;
}

h2 {
  margin: 0;
}

/* Form and Table */
.el-form {
  max-width: 600px;
  margin: 0 auto;
}

.el-table {
  margin-top: 20px;
}
</style>
