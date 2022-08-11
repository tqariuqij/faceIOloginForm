//first we begin by initiallizing next.js

//I will use yarn so

yarn create next-app faceionextjsform

//follow the prompts


//let us then add tailwind css to the project

cd faceionextjsform

yarn add -D tailwindcss postcss autoprefixer

//create taiwind cofugaration fils that is tailwind.config.js and postcss.config.js

yarn tailwindcss init -p

//open tailwindcss.config.js and replace the contents with this


module.exports = {
  content: [
    // add this lines
    "./pages/**/*.{js,ts,jsx,tsx}",
    // if you have other folder, add its path too, like this:
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}


//update ./styles/globals.css. you may delete any other files.
/* ./styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;


go to pages/home and delete all the html and replace it with

 <div className='bg-green-300 text-center text-white h-screen'>
      <Head>
        <title>face.IO login/signup form</title>
        <meta name='description' content='a face.IO login form' />
        <link rel='icon' href='/favicon.ico' />
      </Head>

      <main>
        <h1 className='text-xl font-bold'>home</h1>
      </main>
</div>

//before we run our apllicatin add a new file _document.js to the pages folder. this is used to overide the default `Document` because we want to add a custom script tag to our site. This script tag will enable us to inergrate the faceIO into our project by adding their CDN to the html. if you want to know more you can visit https://faceio.net/integration-guide

import Document, { Html, Head, Main, NextScript } from 'next/document';

class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const initialProps = await Document.getInitialProps(ctx);
    return { ...initialProps };
  }

  render() {
    return (
      <Html>
        <Head></Head>
        <body>
          //note this script tag
          <script src='https://cdn.faceio.net/fio.js'></script>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default MyDocument;

// run yarn dev and confirm that your application looks like this [picture]

yarn dev

inspect your site and confirm whether the faceIO CDN script is located inside the body tag [sreen2]

create a new folder components and add a file SignUpForm.js (components/SignUpForm.js) Since I want to sumarise this as much as possible I have taken the liberty of creating a form feel free to copy the code


import React from 'react';

const SignUpForm = () => {
  return (
    <section className='h-full gradient-form bg-gray-200 md:h-screen'>
      <div className='container py-12 px-6 h-full'>
        <div className=' flex justify-center items-center flex-wrap h-full g-6 text-gray-800'>
          <div className=''>
            <div className='block bg-white shadow-lg rounded-lg'>
              <div className='lg:flex lg:flex-wrap g-0'>
                <div className='px-4 md:px-0'>
                  <div className='md:p-12 md:mx-6'>
                    <div className='text-center'>
                      <h4 className='text-xl font-semibold mt-1 mb-12 pb-1'>
                        Face Authentication by FaceIO
                      </h4>
                    </div>
                    <form>
                      <p className='mb-4'>
                        Please Sign Up if you do not have an account
                      </p>
                      <div className='mb-4'>
                        <input
                          type='email'
                          className='form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none'
                          placeholder='Your Email'
                          name='userEmail'
                        />
                      </div>
                      <div className='mb-4'>
                        <input
                          type='password'
                          className='form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none'
                          placeholder='Password'
                          name='pin'
                        />
                      </div>
                      <div className='text-center pt-1 mb-12 pb-1'>
                        <button
                          className='bg-green inline-block px-6 py-2.5 text-black font-medium text-xs leading-tight uppercase rounded shadow-md hover:bg-blue-700 hover:shadow-lg focus:shadow-lg focus:outline-none focus:ring-0 active:shadow-lg transition duration-150 ease-in-out w-full mb-3'
                          type='button'
                          onClick={handleSubmit}
                        >
                          Sign Up
                        </button>
                      </div>
                      <div className='flex items-center justify-between pb-6'>
                        <p className='mb-0 mr-2'>Do you have an account?</p>
                        <button
                          type='button'
                          className='inline-block px-6 py-2 border-2 border-green-600 text-green-600 font-medium text-xs leading-tight uppercase rounded hover:bg-black hover:bg-opacity-5 focus:outline-none focus:ring-0 transition duration-150 ease-in-out'
                          onClick={handleLogIn}
                        >
                          Log In
                        </button>
                      </div>
                    </form>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  );
};
export default SignUpForm;


//go yo pages/index.js and repalce the h1 tag in yhe main tag with
// remember to import it

<SignUpForm />


//your form should render as this [screen3]


Now go to https://console.faceio.net/ sign up then create a new project by following the steps once you are done you will get a public ID. note it because we will use it here.

//we are going to register a user. In order to do this we must first initilize faceIO object. we will do this inside a useEffect so that even if the state changes it will not reinitialize faceIO object, remember to imprt the useEffect

import React, { useEffect, useState } from 'react';


// initallize faceIO object

let faceio;

  useEffect(() => {
    faceio = new faceIO('xxxxxxxxx');
  }, []);


  //remeber to replace the xxxxxx's with you projct public id


  //in order to register a user we must have user registration logic (just the usual username and password) that will be stored together with the user facial authentication data for future refernce (faceIO does not store acual picture of the user but rather a vector which is hashed and cannot be reverse engineered)

  //let us collect the user data, we are going to use a useState hook (hoks are react.js superpower).

  //import the hook

import React, { useEffect, useState } from 'react';

//we are going to map the values of the username and password into a payload variable object

const [payload, setPayload] = useState({
    userEmail: '',
    pin: '',
  });

  //go to the input field of name userEmail and  update it so that its value is mapped to the userEmail, do the same also to the input field with name pin

  <input        type='email'
                          className='form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none'
                          placeholder='Your Email'
                          name='userEmail'
                          defaultValue={payload.userEmail}
                        />

                        //and
                      
                        <input
                          type='password'
                          className='form-control block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none'
                          placeholder='Password'
                          name='pin'
                          defaultValue={payload.pin}
                        />

 // we need to create an onchange function so that when the user types the info it can be stored in the payload object
 
 const onChange = (e) => {
    setPayload({
      ...payload,
      [e.target.name]: e.target.value,
    });
  };

  //we need to to submit this info to faceIO so that they can sign up the user but we must call faceIO to take the user face authentication so that so that we submit all this together 

  //we will create a sign up function that takes in the payload and enrolls the user to faceIO. note that enroll is a method of the faceIO object.

  const handleSignUp = async (payload) => {
    try {
      let response = await faceio.enroll({
        locale: 'auto',
        payload: payload,
      });

      console.log(` Unique Facial ID: ${response.facialId}
    Enrollment Date: ${response.timestamp}
    Gender: ${response.details.gender}
    Age Approximation: ${response.details.age}`);
    } catch (error) {
      console.log(error);
    }
  };                    

  // let us now create a handleSubmit function that will submit all this info

const handleSubmit = (e) => {
    // prevents the submit button from refreshing the page
    e.preventDefault();

    //just to confirm that payload has the info
    console.log(payload);

    handleSignUp(payload);

  };


  //let us update the sign up button like so

  <button
                          className='bg-green inline-block px-6 py-2.5 text-black font-medium text-xs leading-tight uppercase rounded shadow-md hover:bg-blue-700 hover:shadow-lg focus:shadow-lg focus:outline-none focus:ring-0 active:shadow-lg transition duration-150 ease-in-out w-full mb-3'
                          type='button'
                          onClick={handleSubmit}
                        >
                          Sign Up
                        </button>


 // and as simply as that we have enrolled the user try it and you will see [screen4]
 

 //when that user comes in next time and wants to login he/she will only need to have face authenticated


// let us make the handleLogIn function and update the log in button

const handleLogIn = async () => {
    try {
      let response = await faceio.authenticate({
        locale: 'auto',
      });

      console.log(` Unique Facial ID: ${response.facialId}
        PayLoad: ${response.payload}
        `);
    } catch (error) {
      console.log(error);
    }
  };                        