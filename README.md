 #include "pizza.h"

int Ncook=2; //paraskeuastes
int Noven=15; // fournoi
int Ndeliverer=10; //dianomeis
int Npacker=2; //upalliloi paretarismatos


int success=0; //epituximenes tis paraggelias
int failure=0; //apotuximenes tis paraggelias
int earnings=0;
float avg_wait=0; //o mesos xronos einai anamonis
int max_wait=0; //o megistos xronos einai anamonis
float avg_service=0; //o mesos xronos einai tis epituximenes
int max_service=0; // o megistos{mono gia tis epituximenes}
float avg_cold=0; //o mesos xronos einai oi paraskeuastes{mono epituximenes}
int max_cold=0; //o megistos {epituximenes}

pthread_cond_t cond_cook=PTHREAD_COND_INITIALIZER; 
pthread_cond_t cond_oven=PTHREAD_COND_INITIALIZER; 
pthread_cond_t cond_del=PTHREAD_COND_INITIALIZER; 
pthread_cond_t cond_earn=PTHREAD_COND_INITIALIZER; 
pthread_cond_t cond_avg_wait=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_max_wait=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_avg_service=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_max_service=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_avg_cold=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_max_cold=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_pack=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_success=PTHREAD_COND_INITIALIZER;
pthread_cond_t cond_failure=PTHREAD_COND_INITIALIZER;

pthread_mutex_t lockcook= PTHREAD_MUTEX_INITIALIZER; //lock oi paraskeuastes
pthread_mutex_t lockoven= PTHREAD_MUTEX_INITIALIZER; //lock oi fournoi
pthread_mutex_t lockdel= PTHREAD_MUTEX_INITIALIZER; //lock oi deliverades
pthread_mutex_t lockearn= PTHREAD_MUTEX_INITIALIZER; //lock earnings
pthread_mutex_t lock_avg_wait= PTHREAD_MUTEX_INITIALIZER; //ktl....
pthread_mutex_t lock_max_wait= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lock_avg_service= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lock_max_service= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lock_avg_cold PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lock_max_cold= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lockpack= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t locksuccess= PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t lockfailure=PTHREAD_MUTEX_INITIALIZER;



//-------inputs-------
unsigned int seed;
int Ncust;
//-----------------

int main(int argc, char*argv[])
{
    int rc;
    int i;
    
    int Ncust=atoi(argv[1]); /*
    seed=atoi(argv[2]); //*sporos*/
    
    if(argc!=3){ //o xristis einai na dwsei tis times
          fprintf(stderr,"Invalid number of parametres!");
          return -1;
        }
        
        if(Ncust<0){ //an dwsei arnitiko arithmo pelatwn
           fprintf(stderr,"Invalid number of parametres!");
           return -1;
        }

if (Ncust==0){ // an o arithmos p3latwn einai 0
             printh("No customers!");
             return 0;
        }    
        
        pthread_t threads [Ncust];
        int idArray[Ncust];
        
        for (int i=0;<Ncust;i++){
            idArray[i]=i+1;
            rc=pthread_create(&threads[i],NULL,order,&idArray[i]);
        }   
        
        for (int i=0;<Ncust;i++){
            pthread_join(threads[i],NULL);
        }
        
        avg_cold=avg_cold/success; // mono tis epituximenes paraggelies
        avg_service=avg_service/success; // mono tis epituximenes 
        avg_wait=avg_wait/Ncust; //perilambanontai oles oi paraggelies
        
        printf("The number of successes was %i and the number of failures was %i.\n",success,failure);
        printf("The average waiting time was %f and the max waiting time was %i.\n",avg_wait,max_wait);
        printf("The average service time was %f and the max service time was %i.\n",avg_service,max_service);
        printf("The average pizza colding time was %f and the max time was %i.\n",avg_cold,max_cold);


        pthread_mutex_destroy (&lockcook)
        pthread_mutex_destroy (&lockoven);
        pthread_mutex_destroy (&lockdel);
        pthread_mutex_destroy (&lockearn);
        pthread_mutex_destroy (&lock_avg_wait);
        pthread_mutex_destroy (&lock_max_wait);
        pthread_mutex_destroy (&lock_avg_service);
        pthread_mutex_destroy (&lock_max_service);
        pthread_mutex_destroy (&lock_avg_cold);
        pthread_mutex_destroy (&lock_max_cold);
        pthread_mutex_destroy (&lockpack);
        pthread_mutex_destroy (&locksuccess);
        pthread_mutex_destroy (&lockfailure);
        
        pthread_cond_destroy (&lock_cook);
        pthread_cond_destroy (&lock_del);
        pthread_cond_destroy (&lock_oven);
        pthread_cond_destroy (&lock_earn);
        pthread_cond_destroy (&lock_avg_wait);
        pthread_cond_destroy (&lock_max_wait);
        pthread_cond_destroy (&lock_avg_service);
        pthread_cond_destroy (&lock_max_service);
        pthread_cond_destroy (&lock_avg_cold);
        pthread_cond_destroy (&lock_max_cold);
        pthread_cond_destroy (&lock_pack);
        pthread_cond_destroy (&lock_success);
        pthread_cond_destroy (&lock_failure);   
        
        return 0;
        
        }
        
        void *order(void *x){
            int id= *(int*) x;
            int wait_time;
            int prep_time;
            int deliver_time;
            int order_time;
            int cold_time;
            struct timespes start_order, finish_order;
            struct timespes start_wait, finish_wait;
            struct timespes start_cold, finish_cold;
            struct timespes start_prep, finish_prep;
            struct timespes start_del, finish_del;
          int rc;
            unsigned int payment_delay;
            unsigned int plain;
            unsigned int call_delay;
            unsigned int delivery;
            
            clock_gettime(CLOCK_REALTIME, &start_order);
            
            if (id!=1){
                call_delay=rand_r(&seed) % Torderhigh + Torderlow;
            }
            else{call_delay=0;}   
            
            sleep(call_delay);
            
            clock_gettime(CLOCK_REALTIME,&start_wait);
            
            rc=pthread_mutex_lock(&lockcook);
            while(Ncook==0){
                rc=pthread_cond_wait(&cond_cook_&lockcook);
                
            }
            Ncook--;
            rc=pthread_mutex_unlock(&lockcook);
            
            clock_gettime(CLOCK_REALTIME,&finish_wait);
            wait_time=finish_wait.tv_sec - start_wait.tv_sec;
            
            if(wait_time>max_wait){
                rc=pthread_mutex_lock(&lock_max_wait);
                max_wait=wait_time;
                rc=pthread_mutex_unlock(&lock_max_wait);
            }
            
            rc=pthread_mutex_lock(&lock_avg_wait);
            avg_wait+=(float) wait_time;
            rc=pthread_mutex_unlock(&lock_avg_wait); 
            
            plain=rand_r(&seed) %Norderhigh + Norderlow;

 if((rand_r(&seed)%100)<5){
                rc=pthread_mutex_lock(&lockfailure);
                failure++;
                rc=pthread_mutex_unlock(lockfailure);
                pthread_exit(NULL);
            }
            
            payment_delay=rand_r(&seed) % Tpaymenthigh + Tpaymentlow;
            sleep(payment_delay);
            
            rc=pthread_mutex_lock(&lockcook);
            Ncook++;
            rc=pthread_cond_signal(&lock_cook);
            rc=pthread_mutex_unlock(&lockcook); 
            
            printf("order with id %i has been registered.\n",id);
            
            rc=pthread_mutex_lock(&lockcook);
            earnings+=Cplain*plain;
            rc=pthread_mutex_unlock(&lockearn);
            
            rc=pthread_mutex_lock(&locksuccess);
            success++;
            rc=pthread_mutex_unlock(&locksuccess);
            
            clock_gettime(CLOCK_REALTIME,&start_prep);
            
            
            rc=pthread_mutex_lock(&lockcook);
            while(Ncook==0){
                rc=pthread_cond_wait(&cond_cook_lockcook);
            }
            Ncook--;
            rc=pthread_mutex_unlock(&lockcook);
            
            sleep(plain*Tprep);
            
            rc=pthread_mutex_lock(&lockoven);
            while(Noven<plain){
                rc=pthread_cond_wait(&cond_oven_lockoven)
            }
            Noven=Noven - plain;
            rc=pthread_mutex_unlock(&lockoven);
            
            rc=pthread_mutex_lock(&lockcook);
            Ncook++;
            rc=pthread_cond_signal(&lock_cook);
            rc=pthread_mutex_unlock(&lockcook);
            
            
            sleep(plain*Tbake);
            
            clock_gettime(CLOCK_REALTIME,&start_cold);
            
            rc=pthread_mutex_lock(&lockpack);
            while(Npack==0){
                rc=pthread_cond_wait(&lcond_pack,&lockpack);
            }
            Npack--;
            rc=pthread_mutex_unlock(&lockpack); 
            
            int unpacked=plain;
            while (unpacked>0){
                rc=pthread_mutex_lock(&lockoven);
                Noven++;
                rc=pthread_cond_signal(&cond_oven);
                rc=pthread_mutex_unlock(&lockoven);
                sleep(Tpack);
                unpacked--;
           }
           
           clock_gettime(CLOCK_REALTIME,&finish_prep);
           prep_time=finish_prep.tv_sec - start_prep.tv_sec;
           printf("oder with id %i has been prepared in %i minutes\n",id,prep_time);
           
           rc=pthread_mutex_lock(&lockpack);
           Npack++;
           rc=pthread_cond_signal(&cond_pack);
           rc=pthread_mutex_unlock(&lockpack); 
           
           clock_gettime(CLOCK_REALTIME,&start_del);
           
           rc=pthread_mutex_lock(&lockdel);
           while(Ndeliverer==0){
               rc=pthread_cond_wait(&cond_del_lockdel);
           }
           Ndeliverer--;
           rc=pthread_mutex_unlock(&lockdel);
           
           delivery=rand_r(&seed) % Tdelhigh + Tdellow;
           
           sleep(delivery);
           
           clock_gettime(CLOCK_REALTIME,finish_del);
           clock_gettime(CLOCK_REALTIME,finish_cold);
           clock_gettime(CLOCK_REALTIME,finish_order);
           
           deliver_time=finish_del.tv_sec - start_del.tv_sec;
           printf("order with id %i has beed delivered in %i minutes.\n",id,deliver_time);
           
           cofd_time=finish_cold.tv_sec - start_cold.tv_sec;
           if(code_time>max_cold){
               rc=pthread_mutex_lock(&lock_max_cold);
               max_cold=cold_times;
               rc=pthread_mutex_unlock(&lock_max_cold);
           }
           
           rc=pthread_mutex_lock(&lock_avg_cold);
           avg_cold+=(float)cold_time;
           rc=pthread_mutex_unlock(&lock_avg_cold);
           
           order_time=finish_order.tv_sec - start_order.tv_sec;
           if(order_time>max_service){
               rc=pthread_mutex_lock(&lock_max_service);
               max_service=order_time;
               rc=pthread_mutex_unlock(&lock_max_service);
           }
           
           rc=pthread_mutex_lock(&lock_avg_service);
           avg_service+=(float) order_time;
           rc=pthread_mutex_unlock(&lock_avg_service);
           
           sleep(delivery);
           
           rc=pthread_mutex_lock(&lockdel);
           Ndeliverer++;
           rc=pthread_cond_signal(&cond_del);
           rc=pthread_mutex_unlock(&lockdel);
           
           pthread_exit{NULL};
    }
