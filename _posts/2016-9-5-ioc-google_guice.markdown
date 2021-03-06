---
layout: post
title:  "google guice 用法介绍"
date:   2016/9/5 17:45:21 
---

# google guice 用法介绍 #

## TransactionLog.java ## 
    public class TransactionLog {
    	public void print_info(){
    		System.out.println("this is TransactionLog!");
    	}
    }

## CreditCardProcessor.java ##
    public class CreditCardProcessor {
    	public void print_info(){
    		System.out.println("this is TransactionLog!");
    	}
    }

## DatabaseTransactionLog.java ##
    public class DatabaseTransactionLog extends TransactionLog{
    	public void print_info(){
        	System.out.println("DatabaseTransactionLog");
    	}
	}

## PaypalCreditCardProcessor.java ##
	public class PaypalCreditCardProcessor extends CreditCardProcessor{
    	public void print_info(){
        	System.out.println("PaypalCreaditCardProcessor!");
    	}
	}
## RealBillingService.java ##
    	class RealBillingService {
    	private final CreditCardProcessor processor;
    	private final TransactionLog transactionLog;
    
    	@Inject
    	RealBillingService(CreditCardProcessor processor,
       TransactionLog transactionLog) {
    		this.processor = processor;
    		this.transactionLog = transactionLog;
    	}
    
    	public void chargeOrder() {
    	processor.print_info();
    	transactionLog.print_info();
    	}
    
    	}

## BillingModule.java ##
	package Ioc_guice;

	import com.google.inject.AbstractModule;
	import com.google.inject.Guice;
	import com.google.inject.Injector;

	/**
	 * Created by song on 2016/9/5.
	 */
	public class BillingModule extends AbstractModule {
    	@Override
    	protected void configure() {

     	/*
      	* This tells Guice that whenever it sees a dependency on a TransactionLog,
      	* it should satisfy the dependency using a DatabaseTransactionLog.
      	*/
        	bind(TransactionLog.class).to(DatabaseTransactionLog.class);

     	/*
      	* Similarly, this binding tells Guice that when CreditCardProcessor is used in
      	* a dependency, that should be satisfied with a PaypalCreditCardProcessor.
      	*/
        	bind(CreditCardProcessor.class).to(PaypalCreditCardProcessor.class);
    	}

    	public static void main(String[] args) {
    	/*
     	* Guice.createInjector() takes your Modules, and returns a new Injector
     	* instance. Most applications will call this method exactly once, in their
     	* main() method.
     	*/
    	Injector injector = Guice.createInjector(new BillingModule());
    
    	/*
     	* Now that we've got the injector, we can build objects.
     	*/
    	RealBillingService billingService = injector.getInstance(RealBillingService.class);
    	billingService.chargeOrder();
    	}
    
    }
   