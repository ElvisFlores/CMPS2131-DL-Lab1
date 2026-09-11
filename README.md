# CMPS2131 — Lab 3: A Token Linked List in C23

	They say: That the list.h states that whenever we pass a token into any of functions like push_front , push_back , or interset_after , the ownership of its heap allocated lexeme is transferred to the list . The caller should not be able to read , modify or free that string afterwards. If the insertion were to fail , the list function is the one responsible for cleaning up the token rather than giving that responsibility back to the caller.	
	I say : As i Traced a single token from creation to its destruction it shows  the path one token takes , and why the code follows that rules
	When token_make created a token , it allocated the lexeme strings onto the heap memory and returned the token struct by value . As that was happening , the caller owns that memory block . When the caller later passes that token into list_push_back , the struct is copied by each value onto the functions stack frame . Then the pointer address inside is copied , so basically is passing the ownership to list_push_back.
	But before list_push_back tries to allocate a new node. If malloc fails or the list pointer is a nullptr , the function then calls token_free(&token) which immediately returns a false. This step is important as if malloc were to fail and we returns a fails but without freeing the token this will make the lexeme pointer unreachable.That then creates a memory leak that triggers a out of memory errors later on which means the code could be working right now but later own due to heavy stress/usage the code will fail.
	When malloc passed , node->token = token saves the struct inside the node . Once linked into the list by head or tail , the node becomes the owner of that lexme. The caller local variable still holds a pointer value, but by the rules the caller must never access it again 
	While the token still lives inside the list , functions like list_print and list_to_dot traverse the chain and read the token.lexeme. Since reading a string isn't taking ownership , these functions can just borrow the pointer without changing anything.
	The memory is either release in list_remove_frist, where its unlinked the node from the chain, run token_free(&curr->token) to free the string , and then call free(curr) to release the node struct. In list_destory we traverse the list and save current->next, call token_free(current->token) and then free(current) for each item . in both cases the lexeme is freed once by whichever holds the active node
	

 

