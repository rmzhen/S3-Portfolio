# Software quality
## Table of content
- [Intro](#intro)
- [Implementation](#implementation)
	- [Integration tests](#integration-tests)
		- [Integration testing within Angular](#integration-testing-within-angular)
		- [Integration testing within Spring Boot](#integration-testing-withing-spring-boot)
	- [Unit tests](#unit-tests)
		- [Unit testing within Angular](#unit-testing-within-angular)
		- [Unit testing within Spring Boot](#unit-testing-within-spring-boot)
	- [Static code analysis](#static-code-analysis)

## Intro
In semester 3 we are tasked with implementing tooling and methodology regarding unit integration, regression and system tests, with attention for security, performance aspects, static code analysis and code reviews.

## Implementation

### Integration tests
To test if all the components within a project works together as intended, we can use integration tests to test it. In these tests, mocking a service's response is necessary to avoid dependency of other services in the tests.

#### Integration testing within Angular
The goal of integration testing in the frontend is to ensure that different parts of the app can work together. To do this, the `Jasmine` framework can be used to write tests, which uses the `Karma` framework. You can use `TestBed` to inject dependencies within a given test and use a `spy` to check if a specific function from an outside module has been successfully called. An example of such a test can be seen below:
```js
beforeEach(async () => {
    mockSongService = jasmine.createSpyObj('SongService', ['addSong']);
    await TestBed.configureTestingModule({
      declarations: [ManualAddSongModalComponent],
      providers: [
        HttpClient,
        HttpHandler,
        AppComponent,
        ManualAddSongModalComponent,
        SongListComponent,
        NotificationToastComponent,
        { provide: SongService, useValue: mockSongService },
      ],
      imports: [
        ToastrModule.forRoot({
          timeOut: 10000,
          positionClass: 'toast-bottom-right',
        }),
      ],
    }).compileComponents();  

    fixture = TestBed.createComponent(ManualAddSongModalComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });  

  it('should call notification toast with valid entry', () => {
    let mockNotificationToast = TestBed.inject(NotificationToastComponent);
    notificationSpy = spyOn(mockNotificationToast, 'showNotification');
    mockNotificationToast = jasmine.createSpyObj('NotificationToastComponent', [
      'showNotification',
    ]);
    const mockNewSong: Song = new Song(
      'song1',
      'artist1',
      'duration',
      'coverArt'
    );
    const mockResponse: Song = new Song(
      'song1',
      'artist1',
      'duration',
      'coverArt',
      1
    );
    addSongSpy = mockSongService.addSong
      .withArgs(mockNewSong)
      .and.returnValue(of(mockResponse));
    component.song = mockNewSong;
    component.addSongEntry();
    fixture.detectChanges();

    expect(addSongSpy).toHaveBeenCalled();
    expect(notificationSpy).toHaveBeenCalled();
  }); 

  it('should call notification toast with invalid input', () => {
    let mockNotificationToast = TestBed.inject(NotificationToastComponent);
    notificationSpy = spyOn(mockNotificationToast, 'showNotification');
    mockNotificationToast = jasmine.createSpyObj('NotificationToastComponent', [
      'showNotification',
    ]);
    const mockNewSong: Song = new Song('song1', 'artist1', 'duration', '');
    addSongSpy = mockSongService.addSong
      .withArgs(mockNewSong)
      .and.returnValue(
        throwError(() => new HttpErrorResponse({ status: 400 }))
      );
    component.song = mockNewSong;
    component.addSongEntry();
    fixture.detectChanges();
  
    expect(addSongSpy).toHaveBeenCalled();
    expect(notificationSpy).toHaveBeenCalled();
  });
```

#### Integration testing within Spring Boot
For integration testing in Spring Boot within the project, we want to test if the APi works with the frontend of the project. To do this, the `JUnit` framework is used as the testing framework and `Mockito` to mock responses from services outside of the API that's being tested. An example of such a test can be seen below:
```java
@Test  
@WithMockJwtAuth  
public void addSong_correctlyAddedSong()  
    throws Exception {  
    Song song = new Song("songName", "artistName", "1:23", "url");  
  
    given(service.addSong(song)).willReturn(song);  
  
    mvc.perform(MockMvcRequestBuilders.post("/song/add")  
                    .with(csrf())  
                    .content(toJson(song))  
                    .contentType(MediaType.APPLICATION_JSON))  
            .andExpect(status().isCreated());  
}
```

### Unit tests
To ensure that our code functions correctly, unit tests are implemented for the code that is relevant to realising the user stories for the project. 

#### Unit testing within Angular
For unit testing within Angular, we want to test if a specific part of the app functions as expected without any dependencies on other modules. Just like with integration testing, `Jasmine` can be used to write the tests. An example can be seen below:
```js
beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [NotificationToastComponent],
      providers: [{ provide: toastrService, useValue: ToastrService }],
      imports: [
        ToastrModule.forRoot({
          timeOut: 10000,
          positionClass: 'toast-bottom-right',
        }),
      ],
    }).compileComponents();
    toastrService = TestBed.inject(ToastrService);
    fixture = TestBed.createComponent(NotificationToastComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
 it('returns success notification', () => {
    spyOn(toastrService, 'success');
    let song: Song = new Song('song1', 'artist1', 'duration', 'coverArt', 1);
    component.showNotification(200, song);
    expect(toastrService.success).toHaveBeenCalledWith(
      'Song with name \x22' +
        song!.name +
        '\x22 and artist \x22' +
        song!.artist +
        '\x22 has been successfully added. Please refresh the page.',
      'Success',
      Object({ closeButton: true })
    );
  });
```

#### Unit testing within Spring Boot
As the Spring Boot applications within the Shazzboard project do not contain any logic, it doesn't have any code that needs unit testing.

### Static code analysis
Static code analysis is a way of testing a program without executing it. These analyses focus mainly on the code itself on factors like programming errors, coding standard violations, code smells and security violations. For the Shazzboard project, SonarCloud is used for the Angular application and Codiga for the Spring Boot application.

My initial idea was to use SonarCloud for the entirety of the Shazzboard project, but SonarCloud's documentation states that it has compatibility issues with Maven Java projects, which my backend API uses. After trying to implement it, I indeed ran into issues with implementing it with my Spring Boot application, so as an alternative I chose to use Codiga for its static code analysis.