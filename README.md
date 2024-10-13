# Django REST API Documentation

## Project Overview

This project, called `newproject`, provides a simple API for managing `User` objects. The `User` model contains two fields: `name` and `age`. The API supports basic CRUD operations such as listing users, creating new users, retrieving, updating, and deleting individual users.

The API is built using Django and Django Rest Framework (DRF). It defines the following core components:
- **Models**: Defines the structure of the User model.
- **Serializers**: Responsible for transforming the model data to/from JSON.
- **Views**: Contains logic for processing API requests.
- **URLs**: Maps the API endpoints to the appropriate views.

## File Structure

1. **models.py**: Defines the `User` model with two fields: `name` and `age`.
2. **serializer.py**: Defines the `UserSerializer` to serialize/deserialize `User` objects.
3. **views.py**: Contains the views that handle `GET`, `POST`, `PUT`, and `DELETE` requests for user management.
4. **urls.py** (for the app `api`): Maps the API endpoints to view functions.
5. **main urls.py**: Includes the `api/` namespace in the project-level URLs.

## Models

### User Model (models.py)
```python
class User(models.Model):
    age = models.IntegerField()
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```

**age**: An integer field that stores the user's age.
**name**: A character field (max length: 100) that stores the user's name.

## Serializers

### UserSerializer (serializer.py)
```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'
```

- The **UserSerializer** automatically serializes all fields (age and name) of the User model.
- It is used to convert model instances into JSON and to validate and deserialize data into a User instance.

## Views

### get_user View (views.py)
```python
@api_view(['GET'])
def get_user(request):
    users = User.objects.all()
    serializer = UserSerializer(users, many=True)
    return Response(serializer.data)
```

- **Method**: GET
- **Description**: Returns a list of all users in the database.
- **Response**: A JSON array of user objects. Each object contains name and age fields.

Example response:
[
    {
        "name": "Pedro",
        "age": 38
    },
    ...
]

### create_user View (views.py)
```python
@api_view(['POST'])
def create_user(request):
    serializer = UserSerializer(data=request.data)
    if serializer.is_valid():
        serializer.save()
        return Response(serializer.data, status=status.HTTP_201_CREATED)
    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

- Method: POST
- Description: Creates a new user based on the request data.
- Request Body: A JSON object with name and age fields.
- Response: Returns the newly created user object with a status code 201 Created if the data is valid. If not, returns validation errors with status 400 Bad Request.

Example request:
{
    "name": "Pedro",
    "age": 23
}

### user_detail View (views.py)
```python
@api_view(['GET', 'PUT', 'DELETE'])
def user_detail(request, pk):
    try:
        user = User.objects.get(pk=pk)
    except User.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == "GET":
        serializer = UserSerializer(user)
        return Response(serializer.data)

    if request.method == "PUT":
        serializer = UserSerializer(user, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == "DELETE":
        user.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

- Methods: GET, PUT, DELETE
- Description: Handles retrieving, updating, and deleting a specific user by its primary key (pk).

GET /users/<pk>

- Description: Retrieves a specific user by its pk.
- Response: Returns the user object.

Example response:
{
    "name": "Pedro",
    "age": 23
}

PUT /users/<pk>

- Description: Updates a specific user. The request body should include the updated name and age.
- Request Body: A JSON object with updated name and age.
- Response: Returns the updated user object if the request is valid.

Example request:
{
    "name": "John",
    "age": 30
}

DELETE /users/<pk>

    Description: Deletes a specific user by its pk.
    Response: Returns a status of 204 No Content on successful deletion.

## URLs

### App-level URLs (urls.py)
```python
urlpatterns = [
    path('users/', get_user, name='get_user'),
    path('users/create/', create_user, name='create_user'),
    path('users/<int:pk>', user_detail, name='user_detail')
]
```
- /users/ (GET): Retrieves a list of all users.
- /users/create/ (POST): Creates a new user.
- /users/<int:pk> (GET, PUT, DELETE): Retrieves, updates, or deletes a user based on their primary key (pk).

### Project-level URLs (newproject/urls.py)
```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls'))
]
```
This includes the app-level URLs under the /api/ path.

### Error Handling
- 404 Not Found: If a user with the specified pk does not exist in the user_detail view, a 404 response is returned.
- 400 Bad Request: If invalid data is provided to the create_user or user_detail views, a 400 response is returned with detailed validation errors.

### Example API Requests

1- GET all users:
- URL: /api/users/
- Method: GET

2- Create a user:
- URL: /api/users/create/
- Method: POST
- Request body:

{
    "name": "Alice",
    "age": 28
}

3- Retrieve a specific user:

- URL: /api/users/1
- Method: GET

4- Update a specific user:

- URL: /api/users/1
- Method: PUT
- Request body:
{
    "name": "Bob",
    "age": 30
}

5- Delete a specific user:
- URL: /api/users/1
- Method: DELETE














