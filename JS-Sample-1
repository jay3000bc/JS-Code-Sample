var express = require('express');
const user = require('../models/users.js');

//const app = express();

const passport = require('./passport.js'); //This file already includes passport and passport-local
const util = require('util');
var router = express.Router();

const createUser = user.createUser;
const findOneUser = user.findOneUser;
const findAllUsers = user.findAllUsers;
const isAuthenticated = require('./authenticate.js');


/* GET home page. */
router.get('/', async (req, res, next) => {
  res.render('index', { title: 'Express' });
});

/* GET Signup page */
router.get('/signup', async (req, res, next) => {
  res.render('signup', { title: 'Sign Up' });
});

/* POST Signup page */
router.post('/signup', async (req, res, next) => {
if(req.body.email == '' || req.body.password == '' || req.body.confirmPassword == '' || req.body.mobile == '')
{
  res.render('signup', { 
    title: 'Sign Up',
    errors: "All fields are required",
    email: req.body.email,
    mobile: req.body.mobile
  });
}
else
{
  try
  {
      let result = await createUser(req.body.email, req.body.password, req.body.mobile);
      //Flash Message
      if(result)
        res.redirect("/signin");
  }
  catch(err)
  {
    err.description =  "Something went wrong in DB";
    next(err);
  }
}
});

/* GET Signin page */

  
router.get('/home', isAuthenticated, async (req, res, next) => {
  var io = req.app.get('io');
  try{
      let result = await findAllUsers();
      if(result)
      {
        io.on('connection',(socket) => {
        res.render('home', {users: result});
        });
      }
  }
  catch(err){
    err.description =  "Something went wrong in DB";
    next(err);
  }
  
});
  

/* GET Signin page */
router.get('/signin', async (req, res, next) => {
  if(req.query.prop == 'invalid')
  {
    var message = {
      message: 'Incorect email or password'
    }
  }
  res.render('signin', {message: message});
});

/* POST Signin page */
router.post('/signin', passport.authenticate('local', { 
  failureRedirect: '/signin?prop='+encodeURIComponent('invalid'),
  successRedirect: '/home',
  failureMessage: true
}), (err, req, res, next) => {
  if(err)
    next(err);
});

router.get('/logout', function (req, res){
  req.session.destroy(function (err) {
    res.redirect('/signin'); //Inside a callback… bulletproof!
  });
});

module.exports = router;
