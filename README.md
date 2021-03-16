# CodeIgniter-CRUD-operations-with-MySQL
CRUD means to create, read, update, and delete. If you want to create a CRUD operation in CodeIgniter, configure the first database, and connect the MySQL database after creating the CRUD operation in CodeIgniter.

..............................Follow the instruction step by step to create crud system easily.......................................
Step 1:  
Create Database & table
First, we need a database then create a table I am creating the "test" database and "register" table you can execute the following code to create a database table in your PHPMyAdmin.

CREATE TABLE IF NOT EXISTS `register` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(64) NOT NULL,
  `last_name` varchar(64) NOT NULL,
  `address` text NOT NULL,
  `email` varchar(64) NOT NULL,
  `mobile` varchar(12) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=15;


Step 2: 
Connect to Database
Go to the config folder and open database.php and put your hostname, database username, database password, and database name.

application/config/ database.php

<?php

$db['default'] = array(
    'dsn' => '',
    'hostname' => 'localhost', // host name
    'username' => 'root', //database username
    'password' => '', // database password
    'database' => 'test', // db name
    'dbdriver' => 'mysqli',
    'dbprefix' => '',
    'pconnect' => FALSE,
    'db_debug' => (ENVIRONMENT !== 'production'),
    'cache_on' => FALSE,
    'cachedir' => '',
    'char_set' => 'utf8',
    'dbcollat' => 'utf8_general_ci',
    'swap_pre' => '',
    'encrypt' => FALSE,
    'compress' => FALSE,
    'stricton' => FALSE,
    'failover' => array(),
    'save_queries' => TRUE
);
?>


Step 3: 
Create a Register Controller.
Create Register controller and add the following methods add, delete, and edit look at the example below.

application/controllers/Register.php 

<?php

defined('BASEPATH') OR exit('No direct script access allowed');

class Register extends CI_Controller {

    public function __construct() {
        parent::__construct();
        $this->load->helper('url');
       $this->load->model('register_model');
       
    }

    public function index() {
        $arrData['register_detail'] = $this->register_model->get_all_register_detail();
        $this->load->view('list', $arrData);
    }

    public function add() {
        if ($this->input->post('btnadd')) {
            $arrData['first_name'] = $this->input->post('txtFname');
            $arrData['last_name'] = $this->input->post('txtLname');
            $arrData['address'] = $this->input->post('txtAddress');
            $arrData['email'] = $this->input->post('txtEmail');
            $arrData['mobile'] = $this->input->post('txtMobile');

            $insert = $this->register_model->insert($arrData);
            if ($insert) {
                redirect('register');
            }
        }
        $this->load->view('add');
    }

    public function edit($id) {
        $arrData['register_detail'] = $this->register_model->get_id_wise_register_detail($id);

        if ($this->input->post('btnEdit')) {
            $editData['first_name'] = $this->input->post('txtFname');
            $editData['last_name'] = $this->input->post('txtLname');
            $editData['address'] = $this->input->post('txtAddress');
            $editData['email'] = $this->input->post('txtEmail');
            $editData['mobile'] = $this->input->post('txtMobile');

            $update = $this->register_model->update($editData, $id);
            if ($update) {
                redirect('register');
            }
        }
        $this->load->view('edit', $arrData);
    }

    public function delete($id) {
        $delete = $this->register_model->delete($id);
        if ($delete) {
            redirect('register');
        }
    }

}


Step 4: 
Create Register_model Model
Create Register_model Model and add the following methods get_all_register_detail,get_id_wise_register_detail,insert,update and delete.

application/models/Register_model.php

<?php

defined('BASEPATH') OR exit('No direct script access allowed');

class Register_model extends CI_Model {

    public function __construct() {
        $this->load->database('default');
        $this->load->library('session');

        // Call the Model constructor
        parent::__construct();
    }

    public function get_all_register_detail() {
        $this->db->select('*');
        $this->db->from('register');
        $objQuery = $this->db->get();
        return $objQuery->result_array();
    }

    public function get_id_wise_register_detail($id) {
        $this->db->select('*');
        $this->db->from('register');
        $this->db->where('id', $id);
        $objQuery = $this->db->get();
        return $objQuery->result_array();
    }

    public function insert($arrData) {
        if ($this->db->insert('register', $arrData)) {
            return true;
        } else {
            return false;
        }
    }

    public function update($editData, $id) {
        $this->db->where('id', $id);

        if ($this->db->update('register', $editData)) {
            return true;
        } else {
            return false;
        }
    }

    function delete($id) {

        if ($this->db->delete('register', array('id' => $id))) {
            return true;
        } else {
            return false;
        }
    }

}

?>


step 5. 
Create Views file
create views list, add and edit to send the data to the controller, and save database our record even you have edited registred users.

list.php
edit.php
add.php

application/views/list.php

<html>
    <head>
        <title>codeigniter Tutorial</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    </head>
    <body>

        <div class="container">
            <div class="row">
                <div class="col-md-2"></div>
                <div class="col-md-8">

                    <h2>List of Registered users</h2>
                    <table class="table">
                        <tr>
                            <td colspan="5" align="right"><a href="<?php echo base_url(); ?>register/add">Add</a></td>
                        </tr>
                        <tr>
                            <td>First Name</td>
                            <td>Last Name</td>
                            <td>Email</td>
                            <td>Mobile</td>
                            <td>Action</td>
                        </tr>
                        <?php
                        foreach ($register_detail as $rg) {
                            ?>
                            <tr>
                                <td><?php echo $rg['first_name']; ?></td>
                                <td><?php echo $rg['last_name']; ?></td>
                                <td><?php echo $rg['email']; ?></td>
                                <td><?php echo $rg['mobile']; ?></td>
                                <td><a  href="<?php echo base_url(); ?>register/edit/<?php echo $rg['id']; ?>">Edit</a> <a  href="<?php echo base_url(); ?>register/delete/<?php echo $rg['id']; ?>">Delete</a></td>
                            </tr>
                            <?php
                        }
                        ?>
                    </table>
                </div>
                <div class="col-md-2"></div>
            </div>
        </div>
    </body>
</html> 


application/views/edit.php


<html>
    <head>
        <title>codeigniter Tutorial</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    </head>
    <body>
        <div class="container">
            <div class="row">
                <div class="col-md-3"></div>
                <div class="col-md-6">

                    <form method="post" name="frmEdit">

                        <div>
                            <h3>Update User</h3>
                        </div>

                        <div class="form-group">
                            <td>First Name</td>
                            <label for="">First Name</label>
                            <input type="text" name="txtFname" value="<?php echo $register_detail[0]['first_name']; ?>" class="form-control">
                        </div>
                        <div class="form-group">
                            <td>Last Name</td>
                            <input type="text" name="txtLname" value="<?php echo $register_detail[0]['last_name']; ?>"  class="form-control"> 
                        </div>
                        <div class="form-group">
                            <label for="">Last Name</label>
                            <textarea name="txtAddress"  class="form-control"><?php echo $register_detail[0]['address']; ?></textarea> 
                        </div>
                        <div class="form-group">
                            <label for="">Email</label>
                            <input type="text" name="txtEmail" value="<?php echo $register_detail[0]['email']; ?>"  class="form-control">
                        </div>
                        <div class="form-group">
                            <label for="">Mobile</label>
                            <input type="text" name="txtMobile" value="<?php echo $register_detail[0]['mobile']; ?>"  class="form-control">
                        </div>
                        <div class="form-group">
                            <input type="submit" value="Edit Now" name="btnEdit" class="btn btn-primary btn-lg">
                        </div>

                    </form>
                    <div class="col-md-3"></div>
                </div>
            </div>
        </div>
    </body>
</html> 


application/views/add.php

<html>
    <head>
        <title>codeigniter Tutorial</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    </head>
    <body>
        <div class="container">
            <div class="row">
                <div class="col-md-3"></div>
                <div class="col-md-6">
                    <form method="post" name="frmAdd" action="">
                        <h3>Add User</h3>

                        <div class="form-group">
                            <label for="">First Name</label>
                            <input type="text" class="form-control" name="txtFname">
                        </div>

                        <div class="form-group">
                            <label for="">Last Name</label>
                            <input type="text" class="form-control" name="txtLname">
                        </div>

                        <div class="form-group">
                            <label for="">Address</label>
                            <input type="text" class="form-control" name="txtAddress">
                        </div>

                        <div class="form-group">
                            <label for="">Email</label>
                            <input type="text" class="form-control" name="txtEmail">
                        </div>

                        <div class="form-group">
                            <label for="">Mobile</label>
                            <input type="text" class="form-control" name="txtMobile">
                        </div>

                        <div class="form-group">
                            <input type="submit" value="Add user now" name="btnadd" class="btn btn-primary btn-lg">
                        </div>

                    </form>
                    <div class="col-md-3"></div>
                </div>
            </div>
        </div>
    </body>
</html> 



.............................................Ok so this is now you can your code......................... 
