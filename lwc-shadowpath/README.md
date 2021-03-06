Chrome Extension to generate CssSelector paths for elements encapsulated by our LWC components' Synthetic Shadow DOM.  
Attempting to reproduce the functionality of Copy > Copy JS Path that Chrome provides for native Shadow DOM

 ### Load as Chrome extension by:
 1. Clone or download this repository from github.com  
 2. Go to chrome://extensions/ and toggle the "Developer mode" button.
 3. Click "Load Unpacked" and navigate to directory /lwc-shadowpath of this repository.
 4. Inspect any element to open Element Inspector, and then open up the LWC ShadowRoot panel by clicking '>>' on the far right.
 5. Select the element that the obsolete Selenium selector used to select.
 6. Copy and paste from the panel's shadowpath field into the console to confirm that it is selecting the desired element (remove the extra surrounding double quotes after pasting).
 7. Copy and paste from the panel's java field into existing java test code to replace the obsolete Selenium selector.

 #### Example 1:
 Before:
 ```
 WebElement el = driver.findElement(By.id("bizz"));
 ```
 After: 
 ```
 String queryString = "return document.querySelector('foo-bar').expandShadow....querySelector('#bizz')"; // Generated by extension.
 WebElement el = (WebElement)((JavascriptExecutor)driver).executeScript(queryString);
 ```
#### Example 2:
PageObject fields using the @FindBy annotation will need to be replaced with methods.  
Before:
```
@FindBy(id="bizz")
WebElement bizz;
```
After:
```
WebElement getBizz(WebDriver driver) {
    String queryString = "return document.querySelector('foo-bar').expandShadow....querySelector('#bizz')"; // Generated by extension.
    return (WebElement)((JavascriptExecutor)driver).executeScript(queryString);
}
```
#### Example 3:
PageObject fields using the @FindBy annotation will need to be replaced with annotation @FindByJS provided by the
Test Drop-in Framework you are looking at.
Before:
```
@FindBy(id="bizz")
WebElement bizz;
```
After:
```
@FindByJS(script="return document.querySelector('foo-bar').expandShadow....querySelector('#bizz')")
WebElement bizz;
```

Note:
You do not need to build this repository to use @FindByJS. Instead add this piece of code to your Maven pom.xml file:
```
<dependency>
  <groupId>com.salesforce.cqe</groupId>
  <artifactId>test-drop-in-framework</artifactId>
  <version>3.0.4</version>
</dependency>
```

Please replace the version with the currently latest one by checking out 
https://repository.sonatype.org/#nexus-search;quick~test-drop-in

### Developing extension
 1. When you update code, to see changes you will need to:
     1. Click reload on the chrome://extensions page
     2. Then dismiss the Developer tools
     3. Then re-inspect an element to reopen the Developer tools
 2. Errors show up in the "LWC ShadowPath Generator" panel of the chrome://extensions page.
 3. Exceptions thrown by devtools.js are not displayed anywhere, but console.log messages show up in the console.

### Sources:
* Devtools Extension Docs: https://developer.chrome.com/extensions/devtools
* https://developer.chrome.com/extensions/devtools_inspectedWindow
* https://developer.chrome.com/extensions/devtools_panels
* Samples by API: https://developer.chrome.com/extensions/samples#search:devtools
* Simple example of Devtools extension: https://chromium.googlesource.com/chromium/src/+/master/chrome/common/extensions/docs/examples/api/devtools/panels/chrome-query
