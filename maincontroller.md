<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class Maincontroller extends CI_Controller {
	
	public function index()
	{
		$this->load->view('admin/index');
	}
	public function category()
	{
		 $edit_id = $this->uri->segment(3);
		$cat['data'] = $this->mainmodel->view_cat();
		if($edit_id!=null){
		$cat['c_data'] = $this->mainmodel->cat_edit_id($edit_id);
		}
		$this->load->view('admin/category',$cat);
		//$this->load->view('admin/category',$edit);
		
	}
	public function add_category()
	{
		$data =  array(
		    'cat_name' => $this->input->post('cat_name'),
			'code' => mt_rand(),
			'cat_image'  =>  "demo.png",
			'cat_status' => '1'
			
		);
	   // $this->load->mainmodel();
		 $ans =  $this->mainmodel->cat_insert($data);
		//echo  $msg = "<script>alert('Category Insert Sucessfully!');</script>";
		//$this->load->view('admin/category');
	}
	public function add_size()
	{
		$data =  array(
		    'size_name' => $this->input->post('size_name'),
			'size_code' => mt_rand()	
		);
	   // $this->load->mainmodel();
		 $ans =  $this->mainmodel->size_insert($data);
		return redirect('maincontroller/size');
	}
	public function size()
	{
		$edit_code = $this->uri->segment(3);
		$size['data'] = $this->mainmodel->view_size();
		if($edit_code!=null){
		  $size['data2'] = $this->mainmodel->show_size($edit_code);	
		}
		$q = $this->load->view('admin/size',$size);
		
	}
	public function edit_category()
	{
		$id = $this->input->post('code');
		$data = array(
		'cat_name' => $this->input->post('cat_name')
		);
		$this->mainmodel->edit_cat($data,$id);
		echo $msg="<script>alert('Category update Successfully!');</script>";
		$this->category();	
	}
	public function del_cat(){
		$id = $this->uri->segment(3);
		$this->mainmodel->del_cat($id);
		echo $msg="<script>alert('Category delete Successfully!');</script>";
		return redirect('maincontroller/category');
	}
	public function del_size(){
		$code  = $this->uri->segment(3);
		print_r($code);
		$q = $this->mainmodel->del_size($code);
		if($q)
		{
			echo $msg="<script>alert('Size delete Successfully!');</script>";
		}
		return redirect('maincontroller/size');
	}
	public function del_color(){
		$id = $this->uri->segment(3);
		$this->mainmodel->del_color($id);
		echo $msg = "<script>alert('Color delete successfully!');</script>";
		return redirect('maincontroller/color');
	}
	public function sub_cat(){
		$edit_id = $this->uri->segment(3);
		$subcat['data2'] = $this->mainmodel->view_cat();
		$subcat['data3'] = $this->mainmodel->view_subcat();
		if($edit_id!=null){
		$subcat['sub_data'] = $this->mainmodel->subcat_edit_id($edit_id);
		}
		$this->load->view('admin/sub_cat',$subcat);
	}
	public function add_subcat(){
		$data=array(
		 'cat_code'=>$this->input->post('cat_name'),  
		 'subcat_name'=>$this->input->post('subcat_name'),
		 'subcat_code'=>mt_rand(),
		 'subcat_image'=> "demo.jpg",
		 'subcat_status'=> '1'
		);
		$this->mainmodel->add_subcat($data);
		$this->session->set_flashdata('msg','SubCategory Inserted successfully!');
		return redirect('maincontroller/sub_cat');	
	}
	public function add_color(){
		$data = array(
		'col_name' => $this->input->post('col_name'),
		'col_code' => mt_rand()
		);
		$this->mainmodel->add_color($data);
		$this->session->set_flashdata('msg','Color Insert Successfully!');
		$this->color();		
	}
	public function view_subcat(){
		$subcat['data'] = $this->mainmodel->view_subcat();
		$this->load->view('admin/sub_cat',$subcat);
	}
	public function product()
	{
		$pro_cat['c_data'] = $this->mainmodel->view_cat();
		$pro_cat['sub_data'] = $this->mainmodel->show_subcat();
		$pro_cat['col_data'] = $this->mainmodel->view_color();
		$pro_cat['size_data'] = $this->mainmodel->view_size();
		//print_r($pro_cat);
		$this->load->view('admin/product',$pro_cat);
	}
	public function add_product(){
		 
		$pro_rand = mt_rand();
		$data = array(
		'pro_code' => $pro_rand,
		'cat_code' => $this->input->post('cat_code'),
		'subcat_code' => $this->input->post('subcat_code'),
		'pro_name' => $this->input->post('pro_name'),
		'col_code' => $this->input->post('col_code'),
		'size_code' => $this->input->post('size_code'),
		'price' => $this->input->post('price'),
		'discount' => $this->input->post('discount'),
		'selling_price' => $this->input->post('selling_price'),
		'description' => $this->input->post('description'),
		'full_des' => $this->input->post('full_des'),
		'quantity' => $this->input->post('quantity'),
		'hsmcode' => $this->input->post('hsmcode')
		);
		
		$cpt = count($_FILES['imageupload']['name']);
		
         for($i = 0; $i < $cpt; $i++)
		{
		       $img_name =  $_FILES['file']['name'] = $_FILES['imageupload']['name'][$i]; 
               $_FILES['file']['type'] = $_FILES['imageupload']['type'][$i]; 
               $_FILES['file']['tmp_name'] = $_FILES['imageupload']['tmp_name'][$i];
			   $_FILES['file']['error'] = $_FILES['imageupload']['error'][$i];
			   $_FILES['file']['size'] = $_FILES['imageupload']['size'][$i]; 			   
                // Load and initialize upload library
				
			   $config['upload_path'] = './images/';
               $config['allowed_types'] = 'gif|jpg|png';
               $this->load->library('upload', $config);	   
			   $this->upload->do_upload('file');	
				
               $data2 = array(
				   'img_name' => $img_name,
				   'pro_code' => $pro_rand
			    );	   
				 $this->mainmodel->pro_img($data2); 
			}
			
			    
		        $this->mainmodel->add_product($data);
		
		
		$this->session->set_flashdata('msg','Product Insert Successfully!');
	   return redirect('maincontroller/product');
	}
	
	public function color()
	{	
	    $edit_id=$this->uri->segment(3);
		$col['color'] = $this->mainmodel->view_color();
		if($edit_id!=null){
		$col['col_data'] = $this->mainmodel->col_edit_id($edit_id);
		}
		$this->load->view('admin/color',$col);	
	}
	public function edit_color()
	{
		echo $col_code = $this->input->post('code');
		$data = array(
		'col_name' => $this->input->post('col_name')
		);
		//Print_r($data);
		$t = $this->mainmodel->update_col($col_code,$data);
		if($t)
		{
		echo $msg="<script>alert('Color update Successfully!');</script>";
		}
		$this->color();	
	}
	public function edit_subcat()
	{
		$subcat_code = $this->input->post('code2');
	 	$data = array(
		'subcat_name' => $this->input->post('subcat_name'),
		'cat_code' => $this->input->post('cat_name')
		);
		//print_r($data);
		$this->mainmodel->edit_subcat($data,$subcat_code);
	    echo $msg="<script>alert('SubCategory update Successfully!');</script>";
		return redirect('maincontroller/sub_cat');	
			
	}
	public function edit_size(){
		$edit_code = $this->input->post('code');
		$data = array(
		'size_name' =>$this->input->post('size_name')
		);
		print_r($data);
		$this->mainmodel->edit_size($edit_code,$data);
		return redirect('maincontroller/size');
	}
	public function del_subcat(){
		$id =  $this->uri->segment(3);
		$this->mainmodel->del_subcat($id);
		echo $msg = "<script>alert('Subcategory delete successfully');</script>";	
	}
	public function imageupload(){
		$filesCount = count($_FILES['imageupload']['name']);
        for($i = 0; $i < $filesCount; $i++)
		{
			$_FILES['file']['name']     = $_FILES['imageupload']['name'][$i];
                $_FILES['file']['type']     = $_FILES['imageupload']['type'][$i];
		      $image_name = $_FILES['imageupload']['name'][$i];     
                // Load and initialize upload library
				   $config['upload_path'] = './images/';
                    $config['allowed_types'] = 'gif|jpg|png';
                    $this->load->library('upload', $config);
			if($this->upload->do_upload('imageupload'))
			{
                    // Uploaded file data
                    $fileData = $this->upload->data();
                    $uploadData[$i]['file_name'] = $fileData['file_name'];
                    $uploadData[$i]['uploaded_on'] = date("Y-m-d H:i:s");
            }
		}	
	}
	public function image(){
		$this->load->view('admin/image');
	}
	public function cust_reg(){
		$cust_rand = mt_rand();
		$cust_data = array(
		'cust_code' => $cust_rand,
		'cust_name'  => $this->input->post('cust_name'),
		'cust_email' => $this->input->post('cust_email'),
		'cust_phone' => $this->input->post('cust_phone'),
		'cust_addr' => $this->input->post('cust_addr'),
		'password' => $this->input->post('cust_password'),
		'status' => '1'
		);
	  $this->session->set_flashdata('msg','Color Insert Successfully!');
		
	}
	
	//------------------ FRONT MAINCONTROLLER CODE IS HERE  ----------------------------------//
   
	
	
	
	
	
}
