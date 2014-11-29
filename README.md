HashMap::HashMap(){
	arrSize = 1024;
	arr = new pair[arrSize];
	for(int i = 0; i != arrSize;++i){//sets all slots in the array to empty
		arr[i].is_empty = 1;
	}
	count = 0;
}

HashMap::HashMap(int aSize){
	arrSize = aSize;
	arr = new pair[arrSize];
	for(int i = 0; i != arrSize;++i){//sets all slots in the array to empty
		arr[i].is_empty = 1;
	}
	count = 0;
}

HashMap::~HashMap(){
	delete [] arr;
	arr = NULL;//sets the pointer arr point to nothing
}

HashMap::HashMap(const HashMap& src){
	this->arrSize = src.arrSize;
	this->count = src.count;
	this->arr = new pair[arrSize];
	for(int i = 0; i != arrSize;++i){
		this->arr[i] = src.arr[i];
	}
}

bool HashMap::insert(int key,char& value){
	if(is_full()){
		throw std::overflow_error("The Hash Map is Full");
		return 0;
	}
	else{
		pair item;
		item.k = key;
		item.v = value;
		item.is_empty = 0;
		int slot = hash_function(key);

		if(arr[slot].is_empty == 1){
			arr[slot] = item;
		}
		else{
			while(arr[slot].is_empty != 1){
				slot = probe_linear(slot);
			}
			arr[slot] = item;
		}

		count++;
		return true;
	}
}

bool HashMap::remove(int key,char& value){
	int slot = hash_function(key);
	int actual_slot = -1;
	do{
		if(arr[slot].k == key){
			actual_slot = slot;
		}
		else{
			slot = probe_linear(slot);
		}
	}while( arr[slot].is_empty == 1);
	std::cout << actual_slot;

	if(actual_slot == -1){
		throw std::domain_error("That key does not exist in the Hash Map!");
		return false;
	}

	arr[actual_slot].is_empty = 1;
	count--;
	slot = slot+1;//go to the next slot of the one previously removed!
	while(arr[slot].is_empty == 0){
		int k = arr[actual_slot].k;
		char v = arr[actual_slot].v;
		arr[slot].is_empty = 1;//this is the remove process!!
		count--;
		insert(k,v);
		slot = (slot+1)%arrSize;
	}
	return true;
}

bool HashMap::search(int key,char& value){
	int slot = hash_function(key);
	do{
		if(arr[slot].k == key){
			value = arr[slot].v;
			return true;
		}
		else{
			probe_linear(slot);
		}
	}while(arr[slot].is_empty == 1);
	throw std::domain_error("That key does not exist in the Hash Map!");
}

void HashMap::clear(){
	for(int i = 0; i != arrSize;++i){
		arr[i].is_empty = 0;
	}
}

bool HashMap::is_empty(){
	return (count == 0);
}

size_t HashMap::capacity(){
	return arrSize;
}

size_t HashMap::size(){
	return count;
}

double HashMap::load(){
	double  factor = (double)count/arrSize;
	return factor;
}

bool HashMap::is_full(){
	return (count == arrSize);
}

unsigned int const HashMap::hash_function(int key){
	return key % arrSize;
}

unsigned int const HashMap::probe_linear(int slot){
	return (slot+1)%arrSize;
}


std::ostream& HashMap::print( std::ostream& out) const{
	out << "[ ";
	for(int i = 0; i != arrSize-1;++i){
		if(arr[i].is_empty == 1)
			out << "-";
		else
			out << arr[i].k;
		out << ", ";
	}
	if(arr[arrSize-1].is_empty == 1)
				out << "-";
			else
				out << arr[arrSize-1].k;
			out << "] ";
	return out;
}




