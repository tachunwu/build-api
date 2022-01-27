# build-api

## Resource-oriented design 
### Design flow
* Determine what types of resources an API provides.
* Determine the relationships between resources.
* Decide the resource name schemes based on types and relationships.
* Decide the resource schemas.
* Attach minimum set of methods to resources.

## Resource names
* Use **google.api.http** to **annotate HTTP**
* The first field of Entity use **name**

### Example
```
service LibraryService {
  rpc GetBook(GetBookRequest) returns (Book) {
    option (google.api.http) = {
      get: "/v1/{name=shelves/*/books/*}"
    };
  };
  rpc CreateBook(CreateBookRequest) returns (Book) {
    option (google.api.http) = {
      post: "/v1/{parent=shelves/*}/books"
      body: "book"
    };
  };
}

message Book {
  // Resource name of the book. It must have the format of "shelves/*/books/*".
  // For example: "shelves/shelf1/books/book2".
  string name = 1;

  // ... other properties
}

message GetBookRequest {
  // Resource name of a book. For example: "shelves/shelf1/books/book2".
  string name = 1;
}

message CreateBookRequest {
  // Resource name of the parent resource where to create the book.
  // For example: "shelves/shelf1".
  string parent = 1;
  // The Book resource to be created. Client must not set the `Book.name` field.
  Book book = 2;
}
```

## Standard methods 
### List
```
// Lists books in a shelf.
rpc ListBooks(ListBooksRequest) returns (ListBooksResponse) {
  // List method maps to HTTP GET.
  option (google.api.http) = {
    // The `parent` captures the parent resource name, such as "shelves/shelf1".
    get: "/v1/{parent=shelves/*}/books"
  };
}

message ListBooksRequest {
  // The parent resource name, for example, "shelves/shelf1".
  string parent = 1;

  // The maximum number of items to return.
  int32 page_size = 2;

  // The next_page_token value returned from a previous List request, if any.
  string page_token = 3;
}

message ListBooksResponse {
  // The field name should match the noun "books" in the method name.  There
  // will be a maximum number of items returned based on the page_size field
  // in the request.
  repeated Book books = 1;

  // Token to retrieve the next page of results, or empty if there are no
  // more results in the list.
  string next_page_token = 2;
}
```
### Get
```
// Gets a book.
rpc GetBook(GetBookRequest) returns (Book) {
  // Get maps to HTTP GET. Resource name is mapped to the URL. No body.
  option (google.api.http) = {
    // Note the URL template variable which captures the multi-segment resource
    // name of the requested book, such as "shelves/shelf1/books/book2"
    get: "/v1/{name=shelves/*/books/*}"
  };
}

message GetBookRequest {
  // The field will contain name of the resource requested, for example:
  // "shelves/shelf1/books/book2"
  string name = 1;
}
```
### Create 
```
// Creates a book in a shelf.
rpc CreateBook(CreateBookRequest) returns (Book) {
  // Create maps to HTTP POST. URL path as the collection name.
  // HTTP request body contains the resource.
  option (google.api.http) = {
    // The `parent` captures the parent resource name, such as "shelves/1".
    post: "/v1/{parent=shelves/*}/books"
    body: "book"
  };
}

message CreateBookRequest {
  // The parent resource name where the book is to be created.
  string parent = 1;

  // The book id to use for this book.
  string book_id = 3;

  // The book resource to create.
  // The field name should match the Noun in the method name.
  Book book = 2;
}

rpc CreateShelf(CreateShelfRequest) returns (Shelf) {
  option (google.api.http) = {
    post: "/v1/shelves"
    body: "shelf"
  };
}

message CreateShelfRequest {
  Shelf shelf = 1;
}
```
### Update
```
// Updates a book.
rpc UpdateBook(UpdateBookRequest) returns (Book) {
  // Update maps to HTTP PATCH. Resource name is mapped to a URL path.
  // Resource is contained in the HTTP request body.
  option (google.api.http) = {
    // Note the URL template variable which captures the resource name of the
    // book to update.
    patch: "/v1/{book.name=shelves/*/books/*}"
    body: "book"
  };
}

message UpdateBookRequest {
  // The book resource which replaces the resource on the server.
  Book book = 1;

  // The update mask applies to the resource. For the `FieldMask` definition,
  // see https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#fieldmask
  FieldMask update_mask = 2;
}
```
### Delete
```
// Deletes a book.
rpc DeleteBook(DeleteBookRequest) returns (google.protobuf.Empty) {
  // Delete maps to HTTP DELETE. Resource name maps to the URL path.
  // There is no request body.
  option (google.api.http) = {
    // Note the URL template variable capturing the multi-segment name of the
    // book resource to be deleted, such as "shelves/shelf1/books/book2"
    delete: "/v1/{name=shelves/*/books/*}"
  };
}

message DeleteBookRequest {
  // The resource name of the book to be deleted, for example:
  // "shelves/shelf1/books/book2"
  string name = 1;
}
```
## Custom methods
* Reboot a virtual machine
* Send mail
* Promote an employee
* Batch methods
* Search
* Move
* Cancel
### Example
```
https://service.name/v1/some/resource/name:customVerb

// This is a service level custom method.
rpc Watch(WatchRequest) returns (WatchResponse) {
  // Custom method maps to HTTP POST. All request parameters go into body.
  option (google.api.http) = {
    post: "/v1:watch"
    body: "*"
  };
}

// This is a collection level custom method.
rpc ClearEvents(ClearEventsRequest) returns (ClearEventsResponse) {
  option (google.api.http) = {
    post: "/v3/events:clear"
    body: "*"
  };
}

// This is a resource level custom method.
rpc CancelEvent(CancelEventRequest) returns (CancelEventResponse) {
  option (google.api.http) = {
    post: "/v3/{name=events/*}:cancel"
    body: "*"
  };
}

// This is a batch get custom method.
rpc BatchGetEvents(BatchGetEventsRequest) returns (BatchGetEventsResponse) {
  // The batch get method maps to HTTP GET verb.
  option (google.api.http) = {
    get: "/v3/events:batchGet"
  };
}
```
## Standard fields
Just read the [Table](https://cloud.google.com/apis/design/standard_fields)

## Errors
Just read the [Table](https://cloud.google.com/apis/design/errors)

## Naming conventions
* Product name: NTHU GDSC Drug Store
* Service name: nthu.gdsc.drug.io
* Package name: nthu.gdsc.drug.v1
* Interface name: nthu.gdsc.drug.v1.DrugService
* Check [details](https://cloud.google.com/apis/design/naming_convention)
