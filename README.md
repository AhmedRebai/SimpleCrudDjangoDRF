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









