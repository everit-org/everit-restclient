everit-restclient
=================

Helper classes to make HTTP+JSON based REST calls. The library uses
Jackson for JSON serialization/deserialization.

The original goal of this library to add some functions that help generating
code from OpenAPI specification, so the code inside the generated classes
are much less simple.

## Create a RestClient instance

    // Create a HttpClient instance.
    HttpClient httpClient = ...
    
    // Create a JSONObjectMapper instance. E.g.: based on Jackson or GSON
    JSONObjectMapper objectMapper = ...
    
    // Instantiate the rest client. You must close the instance after it
    // is not necessary anymore. Closing the restClient instance will also
    // close the httpClient instance.
    RestClient restClient = new RestClient(httpClient, objectMapper);


## Sample Rest function

    /**
     * This is a sample rest function
     *
     * @param requestBody
     *     The user to add to the group. (required)
     *
     * @param pathParams
     *     A sample required path parameter. (required)
     *
     * @param queryParam1
     *      A sample optional query parameter. (optional)
     * 
     * @param restRequestEnhancer
     *     Adds the possibility to modify the rest request before sending
     *     out. This can be useful to change the basePath or add
     *     authorizations tokens for example.
     *
     * @return
     *     Some object that will be converted from JSON.
     */
    public Single<MyReturnType> doSomeCall(MyBodyType requestBody,
        Long pathParam1, Optional<String> queryParam1,
        Optional<RestRequestEnhancer> restRequestEnhancer) {
      
      RestRequest.Builder requestBuilder = RestRequest.builder()
          .method(HttpMethod.POST)
          .basePath(DEFAULT_BASE_PATH)
          .path("/rest/api/{pathParam1}");
      
      Map<String, String> pathParams = new HashMap<>();
      pathParams.put("pathParam1", Objects.requireNonNull(pathParam1).toString());
      requestBuilder.pathParams(pathParams);
      
      Map<String, Collection<String>> queryParams = new HashMap<>();
      if (queryParam1.isPresent()) {
        queryParams.put("queryParam1", Collections.singleton(queryParam1));
      }
      requestBuilder.queryParams(queryParams);
      
      Map<String, String> headers = new HashMap<>();
      requestBuilder.headers(headers);
      
      requestBuilder.requestBody(Optional.of(requestBody));
      
      return RestCallUtil.callEndpoint(requestBuilder.build(),
          restRequestEnhancer, new TypeReference<MyReturnType>() {});
    }

For more information, please see the Javadoc of the classes.

## Handling errors

All responses that have status code _>= 400_ are propagated to the return type as _RestException_.

