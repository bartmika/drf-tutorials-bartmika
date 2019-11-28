# Lesson 01 - Homework
## A. Remote Calculator
Create a calculator which will take numbers, perform mathemtical calculations, and return the computation results.

1. Create a code repository named: ``api_remote_calc``

2. Create the project named: ``remote_calc``

3. Write the django server project to implement the following API endpoints:

        /api
        GET
        Returns the version of the calculator
        
        /api/add
        POST
        Adds a number

        /api/result
        GET
        Returns the calculated value

        /api/clear
        POST
        Clears the memory so the value starts at zero

4. Test to confirm your code works using ``HTTPie tool``. Note: Windows users must put ``winpty`` in front of the following

        http http://127.0.0.1:8000/
        http --form POST http://127.0.0.1:8000/add number=1
        http --form POST http://127.0.0.1:8000/add number=1
        http http://127.0.0.1:8000/result
        http http://127.0.0.1:8000/clear
        http http://127.0.0.1:8000/result
        
## B. Remote Statistics
The purpose of this application is to create a statistics calculating microservice which programmers from all around the world can use in their web-applications.

1. Create a code repository named: ``api_remote_stat``

2. Create the project named: ``remote_stat``

3. Write the django server project to implement the following API endpoints:

        /add
        POST
        Adds a number to be used for statistical computations in our app.
        
        /average
        GET
        Returns the average of all the numbers inputted so far.
        
        /mean
        GET
        Returns the mean of all the numbers inputted so far.
        
        /statistics
        GET
        Returns various statistics
        
        /clear
        POST
        Deletes all the numbers submitted in our app.
        
4. Test your API endpoints using the ``HTTPie tool`` app.

## C. Students DB
The purpose of this application is keep a record of students in our school. 

Before you begin, here is an example of useful code to use with your details API endpount (retrieve, update, & delete).

```python
class SnippetDetail(APIView):
    """
    Retrieve, update or delete a snippet instance.
    """
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        snippet = self.get_object(pk)
        # ...
        return Response( # ... )

    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        # ...
        # Note: If validation fails then run the following:
        # return Response(data={"error":"some error"}, status=status.HTTP_400_BAD_REQUEST)
        
        # Else return success.
        return Response( # ... )
        
    def delete(self, request, pk, format=None):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT) 
```

Review the code above and proceed to complete the following.

1. Create a code repository named: ``api_studentdb``

2. Create the project named: ``studentdb``

3. Write the django server project to implement the following API endpoints:

        /add
        POST
        Add a student record
        
        /students
        GET
        Returns the list of students
        
        /student/<id>
        GET
        Returns the details of a student at the ID URL argument.
        
        /student/<id>
        POST
        Updates a student record
        
        /student/<id>
        
        /clear
        POST
        Deletes all the numbers submitted in our app.
        
4. Test your API endpoints using the ``HTTPie tool`` app.

## D. Military Recruits
The purpose of this application is submit prospective recruits and process their applications.

1. Create a code repository named: ``api_mil_recruit``

2. Create the project named: ``milrecruit``

3. Write the django server project to implement the following API endpoints:

        /add
        POST
        Adds a recruite. Please make sure to have the following fields:
        * Name
        * Age
        * Has Glasses
        
        /accepted-recruits
        GET
        Returns a list of all the recurits who are accepted based on the criteria:
        * If they are under 18 then they get rejected
        
        /rejected-recruits
        GET
        Returns a list of all the recurits who are rejected based on the following criteria:
        * If they are under 18 then they get rejected
        
        /potential-airforce-recruits
        GET
        Returns a list of accepted recruits who belong to the Air-Force based on the following criteria:
        * If the recruit does not have glasses
 
4. Test your API endpoints using the ``HTTPie tool`` app.

## E. Account Service
The purpose of this microservice is to handle all account related functions like login, register and profile.

1. Create a code repository named: ``api_account_service``

2. Create the project named: ``account_service``

3. Write the django server project to implement the following API endpoints:

        /login
        POST
        If correct the app will create a session for the user so every API request is authenticated, else return an error.
        
        /register
        POST
        Accepts the following fields and creates an account in our app:
        * first name
        * last name
        * email
        * password
        * username
        
        /profile
        GET
        Returns the user account details.
        
        /change-password
        POST
        Changes the password
        
4. Test your API endpoints using the ``HTTPie tool`` app.
