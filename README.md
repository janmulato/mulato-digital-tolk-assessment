#  Digital Tolk Assessment

# BACKEND

## Thoughts about the code
* It follows the repository pattern using a base repository to inherit general functionalities for re-usability in other repository like booking repository.
* It used the standard MVC pattern from laravel however some logic handled in controllers which should not be the cased. 
* The controller is always returning a success api response 200.
* Some methods do not have the proper comment documentation unlike the other.

### Formatting
* I noticed that for some reason other code snippet does not follow coding standard some condition or loops does have brackets and some does not while some of the conditions are inline with end bracket
```
if () {

} else {

}
``` 
and some are on next line.
```
if () {

}
else {

}
``` 
* While other code snippet does not use brackets at all which is not the case for other code snippet
```
if ()
// condition
```
* The next line after a bracket is not consistent some add next line some does not. 
* Some private function does not start with _ although this heavily depends on the coding standard set by the team usually private function start with _ to easily identify private and public functions.
* Lastly I'm not sure what is the naming convention of the variables some are snake case and some are just simply lowercase.
 
### Excellent Code
* Implementation of repository pattern.
* Standard ussage of MVC pattern.
* Reusable crud.

### OK Code
* It heavily used the isset for checking empty or null value maybe because it was a PHP5+ version however it is not really a priority to refactor this as long as it is currently working it just makes hard to read the code because of multiple if condition.
* Some documentation does not have any message how the method works only their params and returns however those methods are already obvious what it does because of the method name.
* There are tons of conditional queries that make use of isset and checking of values. This is ok for now since it does tha job but can be improve in future implementation.
* Although it follows MVC and Repository pattern one thing I noticed is that, it does not handle error responses maybe that is why all response return has a "status" to tell wether it is "success", "fail" etc.. not really terrible but somehow it is hard to do error handling in the frontend specially when trying to try catch, since it will always return a success response.
* Minimal usage of ternary operator, although not really important it helps code readability.

### Terrible Code
* Logic implemented in controller should be move in repository.
* Coding standard is not followed.
* Validation some insert or update does not utilize the base repository validation instead it used nested if else
```
if (isset($data['due_date']) && $data['due_date'] == '') {
    $response['status'] = 'fail';
    $response['message'] = "Du måste fylla in alla fält";
    $response['field_name'] = "due_date";
    return $response;
} else if (isset($data['other_field']) && $data['other_field'] == ''){
    $response['status'] = 'fail';
    $response['message'] = "Du måste fylla in alla fält";
    $response['field_name'] = "other_field";
    return $response;
}
```
makes the code hard to read, while this can be achieved with the base repository validator and return the error response however it will require some changes on the frontend.
* Usage of string literals for boolean variable and later was not properly check.
* There are leftover commented code which can cause undesirable result later if accidentally uncommented.
* Some boolean variable that uses string literals like "yes" or "no" but was not check during if condition.
```
$by_admin = 'yes';
$by_admin = 'no'
if (by_admin) { //always true
}
```
 that was later used for checking in if condition that will always result to true.
* Usage of string literals for condition like 
```
$oneUser->status == '1' //what is status 1? Pending or Active? etc.
```
which is confusing later on.

### How would have I done it
* Make used of the is() method of the authentication with define custom guards that was also used in booking repository instead of using manual checking of user type to check wether admin or super admin or other user type. 
* If the PHP version is 7+ I will make use of the handy isset operator "??" in the future to avoid a lot of if else and isset method to check for empty value and defining default values. If not then I will just use a ternary operator instead.
* For conditional queries I will make use of "when" method of laravel to minimize usage of isset and used "when" as a substitute. So intead of this example:
```
if (isset($requestdata['expired_at']) && $requestdata['expired_at'] != '') {
    $allJobs->where('expired_at', '>=', $requestdata['expired_at']);
}
if (isset($requestdata['will_expire_at']) && $requestdata['will_expire_at'] != '') {
    $allJobs->where('will_expire_at', '>=', $requestdata['will_expire_at']);
}
```
will turn to
```
$allJobs->when(($requestdata[will_expire_at] ?? null), function($query, $param) {
    $query->where('will_expire_at', '>=', $param)
})->when(($requestdata[expired_at] ?? null), function($query, $param) {
    $query->where('expired_at', '>=', $param)
})
```
and can be implemented through a filter method or something so that other filters could be also generalize.
* Use success and error response so that it can be handled in the frontend and make error handling exception much easier both in frontend and backend. 

### Logic
* The changed status method always update to the current status regardless of the validation.
* Some logic of the method is kind of weird where it repeatedly used "where" to filter some field.
* Some method also does not check if the entry exists or not and continues to save, it might result to error if it does not exist.  