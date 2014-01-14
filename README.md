from [CI, Smarty, jQuery] to [CI, Angular]
==============================

First Step: Remove Smarty

Controller
--
// [CI, Smarty, jQuery] way
$this->parser->parse('dirPath/file.tpl', $data);
// [CI, Angular] way
$this->load->view('dirPath/file.php', $data);
