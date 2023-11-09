  <template>
      <div class="flex">
        <div class="w-1/5 bg-gray-800 h-screen">
          <div class="text-white p-4">
            <h2 class="text-xl font-bold">Trang chủ Admin</h2>
            <div class="user-info">
                <p> User: {{ getUsernameFromSession }} </p>
                <button @click="logout">Logout </button>
    
            </div>
          </div>
          <div class="my-4">
            <router-link to="/fields" class="block text-gray-300 hover:bg-gray-700 py-2 px-4">
              Thống kê các sân bóng
            </router-link>
            <router-link to="/bookingDetail" class="block text-gray-300 hover:bg-gray-700 py-2 px-4">
              Sân đang được đặt
            </router-link>
            <router-link to="/UserManager" class="block text-gray-300 hover:bg-gray-700 py-2 px-4">
              Quản lý người dùng
            </router-link>
            <router-link to="/revenue" class="block text-gray-300 hover:bg-gray-700 py-2 px-4">
              Doanh Thu
            </router-link>
          </div>
        </div>
        <div class="w-4/5 bg-white p-4">
          <!-- Nội dung trang chính -->
    
      <div class="fields-container">
        <h2>Thống kê người dùng</h2>
        <button class="btn btn-primary mb-3" @click="showAddUserModal">Thêm người dùng</button>

        <table>
          <thead>
            <tr>
              <th>Tên người dùng</th>
              <th>Số điện thoại</th>
              <th>Email</th>
              <th>Sân đã đặt</th>
              <th>More</th>

            </tr>
          </thead>
          <tbody>
    <tr v-for="(user, index) in users" :key="index">
      <td>{{ user.fullName }}</td>
      <td>{{ user.phone }}</td>
      <td>{{ user.email }}</td>
      <td>{{ user.bookedField }}</td>
      <td><button class="edit btn" @click="editUser(user)">Edit</button>
        <button class="del btn" @click="deleteUser(user._id)">Delete</button>
      </td>

      
    </tr>
  </tbody>
        </table>
      </div>
    
          
          <router-view>  </router-view>

        </div>
        
          <div class="modal" tabindex="-1" role="dialog" id="addUserModal">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h2 class="modal-title"> <strong> Thêm người dùng mới</strong> </h2>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close" @click="unShowAddUserModal">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <!-- Form để thêm người dùng -->
            <form @submit.prevent="addUser">
    <div class="form-group">
      <label for="fullName">Họ và tên</label>
      <input type="text" class="form-control" id="fullName"  @input="newUser.fullName = $event.target.value" required>
    </div>

    <div class="form-group">
      <label for="email">Email</label>
      <input type="email" class="form-control" id="email"  @input="newUser.email = $event.target.value" required> 
    </div>

    <div class="form-group">
      <label for="password">Mật khẩu</label>
      <input type="password" class="form-control" id="password" @input="newUser.password = $event.target.value" required>
    </div>

    <div class="form-group">
      <label for="phone">Số điện thoại</label>
      <input type="text" class="form-control" id="phone" @input="newUser.phone = $event.target.value" required>
    </div> 
    <button type="submit" class="btn btn-primary add" >Thêm</button>

  </form>
          </div>
          
        </div>
      </div>
    </div>

    
    </div>
    
    
      
    </template>
    
    <script>
    import axios from "axios";

    export default {
      
          computed: {
      getUsernameFromSession() {
          return sessionStorage.getItem('isLoggedIn') === 'true' ? sessionStorage.getItem('userLogin') : 'Chưa đăng nhập';
          // Trả về tên người dùng từ session storage nếu đã đăng nhập, nếu không trả về chuỗi rỗng.
        }
          
        },
      data() {
      return {
        users: [], // Danh sách người dùng từ cơ sở dữ liệu
      };
    },
    data() {
      return {
        users: [],
        newUser: {
          fullName: '',
          email: '',
          password: '',
          role: '',
          phone: ''
        }
      };
    },
    mounted() {
      this.getUsers(); // Gọi hàm getUsers() khi component được mounted
    },
    methods: {
      async getUsers() {
        try {
          const response = await axios.get('http://localhost:5000/api/users'); // Thay thế '/api/users' bằng API endpoint của bạn
          this.users = response.data; // Lưu trữ danh sách người dùng từ cơ sở dữ liệu vào mảng users
        } catch (error) {
          console.error(error);
        }
      },
      editUser(user) {
      // Gửi người dùng được chọn để chỉnh sửa đến trang chỉnh sửa
      this.$router.push({ name: '/EditUser/', params: { id: user._id } });
    },
    async deleteUser(userId) {
      try {
        await axios.delete(`http://localhost:5000/api/users/${userId}`);
        // Xóa người dùng khỏi cơ sở dữ liệu và cập nhật danh sách người dùng
        this.getUsers();
      } catch (error) {
        console.error(error);
      }
    },
    unShowAddUserModal(){
      const modal = document.getElementById('addUserModal');
  modal.classList.remove('show'); // xóa class "show" để hủy hiển thị modal
      
    },
    showAddUserModal() {
      const modal = document.getElementById('addUserModal');
  modal.classList.add('show'); // Thêm class "show" để hiển thị modal
      },
      addUser() {
        // Thực hiện logic để thêm người dùng vào cơ sở dữ liệu
        // Ví dụ sử dụng Axios
        axios.post('http://localhost:5000/api/users', this.newUser)
          .then(response => {
            alert("Thêm người dùng thành công");
            console.log("response.data");
            $('#addUserModal').modal('hide'); // Đóng modal sau khi thêm thành công
            this.getUsers(); // Cập nhật danh sách người dùng
          })
          .catch(error => {
            alert("Thêm người dùng thất bại");
            $('#addUserModal').modal('hide'); // Đóng modal sau khi thêm thành công

            console.error(error);
          });
      },
    
            logout() {
                  
    
                sessionStorage.removeItem('isLoggedIn'); 
                sessionStorage.removeItem('userLogin');
                this.$router.push('/admin');    
    
        }
    },
    };
    
    </script>
    
    <style>
    .fields-container {
      margin-top: 50px;
      margin-bottom: 100px;
    
    }
    .fields-container h2 {
      margin-bottom: 20px;
      font-size: 30px;
    }
    .fields-container table {
      width: 100%;
      border-collapse: collapse;
    }
    .fields-container th,
    .fields-container td {
      border: 1px solid #dddddd;
      padding: 8px;
      text-align: left;
    }
    .fields-container tr:nth-child(even) {
      background-color: #f2f2f2;
    }
    .modal {
      display: none;
      position: fixed;
      justify-content: center;
      z-index: 1;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      overflow: auto;
      background-color: rgba(0, 0, 0, 0.4);
    }
    .modal.show {
  display: block; 
}
.modal-header{
  display:flex;
  justify-content: space-between;

}
.close{
  font-size: large;
  
}
    .modal-content {
      border-radius: 10px;

      background-color: #fefefe;
      margin: 15% auto;
      padding: 20px;
      border: 1px solid #888;
      width: 30%;
    }
    .modal-title{
      font-size:25px;

    
    }

    .form-group {
      margin-bottom: 1rem;
    }

    .form-group label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: bold;
    }

    .form-control {
      display: block;
      width: 100%;
      padding: 0.375rem 0.75rem;
      font-size: 1rem;
      line-height: 1.5;
      color: #495057;
      background-color: #fff;
      background-clip: padding-box;
      border: 1px solid #ced4da;
      border-radius: 0.25rem;
      transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
    }
.add{
  display: flex;
  justify-content: space-around;
  margin-left: 45%;
}
    .form-control:focus {
      color: #495057;
      background-color: #fff;
      border-color: #80bdff;
      outline: 0;
      box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
    }

    .btn {
      display: inline-block;
      font-weight: 400;
      color: #212529;
      text-align: center;
      vertical-align: middle;
      cursor: pointer;
      background-color: #007bff;
      padding: 0.375rem 0.75rem;
      font-size: 1rem;
      line-height: 1.5;
      border-radius: 0.25rem;
      transition: color 0.15s, background-color 0.15s, border-color 0.15s, box-shadow 0.15s;
    }

    .btn:hover {
      background-color: #0056b3;
    }

    .btn-primary {
      background-color: #007bff;
    }

    .btn-primary:hover {
      background-color: #0056b3;
    }
    .btn{
      padding:5px;
      border-radius: 5px;
      background-color: #dddddd;

      margin-right:5px ;
    }
    .btn:hover{
      background-color: #b7b7b7;
      transform: scale(1.1);
    }
    </style>