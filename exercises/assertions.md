# On assertions

Answer the following questions:

1. The following assertion fails `assertTrue(3 * .4 == 1.2)`. Explain why and describe how this type of check should be done.

2. What is the difference between `assertEquals` and `assertSame`? Show scenarios where they produce the same result and scenarios where they do not produce the same result.

3. In classes we saw that `fail` is useful to mark code that should not be executed because an exception was expected before. Find other uses for `fail`. Explain the use case and add an example.

4. In JUnit 4, an exception was expected using the `@Test` annotation, while in JUnit 5 there is a special assertion method `assertThrows`. In your opinion, what are the advantages of this new way of checking expected exceptions?

## Answer

    assertTrue(3 * .4 == 1.2)
	
		Le résultat de 3* .4 contient beaucoup de nombres décimaux 1.2000000000000002
		la machine éffectue les opérations en se basant sur 
		
	b.
		assertEquals affirme que deux objets sont égaux
		assertSame affirme que deux objets font référence au même objet
		
		assertEquals utilise la méthode equals()  pour comparer les objets, tandis que assertSame utilise l'opérateur == pour les comparer.  
		Ainsi, la différence est exactement la même qu'entre == (comparer par valeur) et égal (comparer l'identité).
		
	c.
		-	Le cas d'utilisation habituel est de l'appeler lorsqu'aucune exception n'a été levée dans un test négatif. 
		
			try{
	   			bizMethod(badData);
	   			fail(); // FAIL quand l'exception n'est pas levée
			} catch (BizException e) {
	   			assert(e.errorCode == THE_ERROR_CODE_U_R_LOOKING_FOR)
			}
		
		

		-	Un autre cas d'utilisation, lorsque quelque chose aurait mal tourné dans ma méthode @Before.
			
			public Object obj;

			@Before
			public void setUp() {
			    // il s'agit de notre setup
			    obj = new Object();
			}

			@Test
			public void testObjectManipulation() {
			    if(obj == null) {
				fail("Ne doit pas être null");
			     }
			}
			
		-	

			Dans les paramètres simultanés et/ou asynchrones, vous voudrez peut-être vérifier que certaines méthodes (par exemple les 
			délégués, les écouteurs d'événements, les gestionnaires de réponse, etc.) ne sont pas appelées. Mis à part les cadres 
			moqueurs, vous pouvez appeler fail()dans ces méthodes pour échouer aux tests. Les délais d'attente expirés sont une autre 
			condition de défaillance naturelle dans de tels scénarios.

			Par exemple:

			final CountDownLatch latch = new CountDownLatch(1);

			service.asyncCall(someParameter, new ResponseHandler<SomeType>() {
			    @Override
			    public void onSuccess(SomeType result) {
				assertNotNull(result);
				// Further test assertions on the result
				latch.countDown();
			    }

			    @Override
			    public void onError(Exception e) {
				fail(exception.getMessage());
				latch.countDown();
			    }
			});

			if ( !latch.await(5, TimeUnit.SECONDS) ) {
			    fail("No response after 5s");
			}

	d.	
		Les classes de tests JUnit 5 sont similaires à celles de JUnit 4 : basiquement, il suffit d'écrire une classe contenant des méthodes 
		annotées avec @Test.
		assertThrows(), vous permet de tester plusieurs exceptions dans le même test. Avec la prise en charge des lambdas dans Java 8, 
		c'est le moyen canonique de tester les exceptions dans JUnit.

