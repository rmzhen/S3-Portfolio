# Software quality
## Table of content
- [Intro](#intro)
- [Implementation](#implementation)
	- [Branches](#branches)
		- [GP](#gp)
	- [Unit testing](#unit-testing)
		- [IP](#ip)
		- [GP](#gp)

## Intro
In semester 3 we are tasked with implementing tooling and methodology regarding unit integration, regression and system tests, with attention for security, performance aspects, static code analysis and code reviews.

## Implementation
### Branches
Our GP make use of GitHub for version control. For each new feature that gets implemented, a new branch will be created for it in which that feature will get implemented. When the feature is realised, it will get merged with the main and dev branch via a pull request. 

#### GP
Within the GP, we've set a few rules regarding merging:
- At least 2 people unrelated to the pull request must approve the pull request before this gets merged with the main or dev branch.
- The code must pass a build test.
- The code must pass the Sonarcloud quality check.

### Unit testing
To ensure that our code functions correctly, we've implemented unit tests for the code that is relevant to realising the user stories for the project. 

#### IP
Within my Java APIs, I've made use of the `JUnit` unit testing framework and `Mockito` mocking framework to implement my unit tests. Down below is a snippet of an implemented unit test.
```java
@Test  
public void addSong_correctlyAddedSong()  
    throws Exception {  
    Song song = new Song("songName", "artistName", "1:23", "url");  
  
    given(service.addSong(song)).willReturn(song);  
  
    mvc.perform(MockMvcRequestBuilders.post("/song/add")  
            .contentType(MediaType.APPLICATION_JSON)  
            .content(toJson(song)))  
            .andExpect(status().isOk());  
}  
  
public static String toJson(final Object obj) throws JsonProcessingException {  
    return new ObjectMapper().writeValueAsString(obj);  
}
```

#### GP
Within the React frontend part, we made use of the testing library `jest-dom`  to test our DOM interface of the application. This can be found within the [customer-frontend](https://github.com/Modus-1/customer-frontend) repository within our GP GitHub.