<template>
  <el-container style="height: 100vh;">
    <!-- Header -->
    <el-header style="background-color: #409EFF; color: white; text-align: center;">
      <h2>Product Order Price Application</h2>
    </el-header>

    <el-container>
      <!-- Left Side Menu -->
      <el-aside width="200px" style="background-color: #f2f2f2;">
        <el-menu default-active="1">
          <el-menu-item index="1">Home</el-menu-item>
          <el-menu-item index="2">Check Price</el-menu-item>
        </el-menu>
      </el-aside>

      <!-- Main Section -->
      <el-main>
        <!-- Product Model Form -->
        <el-form ref="productForm" :model="productForm" label-width="200px" style="margin-bottom: 20px;">
          <el-form-item label="Product Model" prop="productModel">
            <el-select v-model="productForm.productModel" placeholder="Select product model" style="width: 100%;">
              <el-option
                v-for="model in productModels"
                :key="model"
                :label="model"
                :value="model"
              ></el-option>
            </el-select>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="fetchOrderDetails">Get Order Details</el-button>
          </el-form-item>
        </el-form>

        <!-- Response Section -->
        <el-table :data="orderDetails" style="width: 100%; margin-top: 20px;">
          <!-- Table Header -->
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="proformaInvoiceNum" label="Invoice Number" width="200"></el-table-column>
          <el-table-column prop="factoryOrderNumber" label="Factory Order Number" width="200"></el-table-column>
          <el-table-column prop="proformaInvoiceDate" label="Invoice Date" width="180">
            <template #default="scope">
              {{ new Date(scope.row.proformaInvoiceDate).toLocaleDateString() }}
            </template>
          </el-table-column>
          <el-table-column prop="productModel" label="Product Model" width="150"></el-table-column>
          <el-table-column prop="unitPriceRMB" label="Unit Price (RMB)" width="150"></el-table-column>
          <el-table-column prop="unitPriceUSD" label="Unit Price (USD)" width="150"></el-table-column>
          <el-table-column prop="invoiceQuantity" label="Invoice Quantity" width="150"></el-table-column>
          <el-table-column prop="factoryQuantity" label="Factory Quantity" width="150"></el-table-column>
          <el-table-column prop="amountUSD" label="Amount (USD)" width="150"></el-table-column>
          <el-table-column prop="amountRMB" label="Amount (RMB)" width="150"></el-table-column>
        </el-table>

        <!-- Error Section -->
        <div v-if="errorMessage" style="margin-top: 20px; color: red;">
          <p>Error: {{ errorMessage }}</p>
        </div>
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
  name: "ProductOrderPrice",
  data() {
    return {
      productForm: {
        productModel: "",
      },
      productModels: [], // List of product models from backend
      orderDetails: [], // Order details, initially empty array
      errorMessage: null,
    };
  },
  methods: {
    async fetchProductModels() {
      try {
        // Fetch product models from backend
        const response = await axios.get(`http://52.77.11.70:8092/proformalInvoice/findProductModels`);
        this.productModels = response.data; // Assuming response.data is a list of strings
      } catch (error) {
        console.error("Error fetching product models:", error);
        this.$message.error("Failed to load product models. Please try again.");
      }
    },
    async fetchOrderDetails() {
      if (!this.productForm.productModel) {
        this.$message.error("Please select a product model!");
        return;
      }

      try {
        // HTTP request with dynamic URL using template literal
        const response = await axios.get(
          `http://52.77.11.70:8092/proformalInvoice/findProformaInvoiceByProductModel/${this.productForm.productModel}`
        );

        // Assume the response contains a list of orders
        this.orderDetails = response.data;
        this.errorMessage = null;

        this.$message({
          message: "Order details fetched successfully!",
          type: "success",
        });
      } catch (error) {
        console.error("Error fetching order details:", error);
        this.errorMessage = "Failed to fetch order details. Please try again.";
        this.orderDetails = [];
      }
    },
  },
  mounted() {
    // Load product models when component is mounted
    this.fetchProductModels();
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

/* Form and Response Section */
.el-form {
  max-width: 600px;
  margin: 0 auto;
}

p {
  font-size: 16px;
}
</style>
