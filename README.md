ZendExt_View_Smarty3
====================

A simple class that could be used as the custom view element in a Zend Framework 1 application
*NOTE*: This class uses the SmartyBC.class.php instead of the normal Smarty.class.php 


Configurations can be set in the application.ini. It accepts all the configurations for the smarty template engine
 and also a few other custom ones. Below is a sample configuration:

; smarty
smarty.caching =0; caching will be done with zend_cache
smarty.cache_lifetime = 14400 ; 4 hours
smarty.compile_dir = APPLICATION_PATH "/../smarty/templates_c/"
smarty.config_dir = ""
smarty.debugging = false
smarty.cache_dir = APPLICATION_PATH "/../smarty/cache/"
smarty.left_delimiter = "{"
smarty.right_delimiter = "}"
smarty.php_handling = 0
smarty.compile_check = FALSE
smarty.force_compile = 0;
smarty.error_reporting = 0;E_ALL
smarty.default_template = "default.tpl"

Also you could do additional configuration inside the Bootstrap.php inside the application folder to swap the 
default Zend_View with this one. Here is a sample code for that

protected function _initView()
{
		$config = $this->getOption('smarty');
		$view = new ZendExt_View_Smarty3(null,$config);
		//setup viewrenderer with the suffix and view
		$viewRenderer = Zend_Controller_Action_HelperBroker::getStaticHelper('viewRenderer');
		$viewRenderer->setViewSuffix('tpl');
		$viewRenderer->setView($view);
		// Set the default pagination controls html file
		Zend_Paginator::setDefaultScrollingStyle('Elastic');
		Zend_View_Helper_PaginationControl::setDefaultViewPartial('includes/controls.tpl');
		return $view;
}

There is an issue with the Zend_Loader_Autloader and the smarty class's autoload. Sometimes they conflict. To avoid 
this issue, instrcut the zend autoloader to NOT attempt autoloading any class starting with "Smarty_" and also 
manually include the smartyBC.class.php file before instantiation the class. Again here is an example

protected function _initAutoload()
  {
		// Add autoloader empty namespace
		$autoloader = Zend_Loader_Autoloader::getInstance();
		$autoloader->registerNamespace(array('ZendExt_'));
		$autoloader->setFallbackAutoloader(true);
		$autoloader->suppressNotFoundWarnings(false);
		/**
		 * Zend’s autoloader is trying to load a class internal
		 * to Smarty’s structure, and since Zend isn’t aware of it,
		 * the autoloader can’t find the class.
		 * The solution is to tell Zend to ignore all classes
		 * beginning with ‘Smarty_’.
		 * The solution is to tell zend autoloader to ignore all
		 * classes starting with Smarty_
		 *
		 */
		$autoloader->pushAutoloader(NULL, 'Smarty_' );
		return $autoloader;
	}

