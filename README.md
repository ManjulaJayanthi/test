# test
hello
use std::fmt::{self, Debug};

use actix_web::{
    body::BoxBody,
    http::{Error as ActixWebError, StatusCode},
    HttpResponse, ResponseError,
};

#[derive(Debug)]
pub enum TestError {
    NotFound { error: String },
    ActixWebErr(ActixWebError),
    UnKnown,
}

impl From<ActixWebError> for TestError {
    fn from(err: ActixWebError) -> Self {
        TestError::ActixWebErr(err)
    }
}

type TestResult<T> = Result<T, TestError>;
pub type TestResponse = TestResult<()>;

impl fmt::Display for TestError {
    fn fmt(&self, _f: &mut fmt::Formatter<'_>) -> fmt::Result {
        todo!()
    }
}

impl ResponseError for TestError {
    fn status_code(&self) -> StatusCode {
        StatusCode::INTERNAL_SERVER_ERROR
    }

    fn error_response(&self) -> HttpResponse<BoxBody> {
        HttpResponse::NotFound()
            .content_type("application/json; charset=utf-8")
            .json({})
    }
}

//     fn error_response(&self) -> HttpResponse<actix_web::body::BoxBody> {
//         let mut res = HttpResponse::new(self.status_code());

//         let mut buf = actix_web::web::BytesMut::new();
//         let _ = std::write!(helpers::MutWriter(&mut buf), "{}", self);

//         let mime = mime::TEXT_PLAIN_UTF_8.try_into_value().unwrap();
//         res.headers_mut().insert(actix_web::http::header::CONTENT_TYPE, mime);

//         res.set_body(actix_web::body::BoxBody::new(buf))
//     }
// }

// impl Responder for TestError {
//     // fn error_response(&self) -> HttpResponse {
//     //     match self {
//     //         TestError::NotFound { error } => HttpResponse::NotFound()
//     //             .content_type("application/json; charset=utf-8")
//     //             .json(error),
//     //         TestError::UnKnown => todo!(),
//     //     }
//     // }

//     // fn status_code(&self) -> StatusCode {
//     //     StatusCode::INTERNAL_SERVER_ERROR
//     // }

//     type Body = ();

//     fn respond_to(self, req: &actix_web::HttpRequest) -> HttpResponse<Self::Body> {
//        match self {
//         TestError::NotFound { error } =>HttpResponse::NotFound()
//                     .content_type("application/json; charset=utf-8")
//                     .json(error)
//         TestError::UnKnown => todo!(),
//            }

//     }

//     // fn customize(self) -> actix_web::CustomizeResponder<Self>
//     // where
//     //     Self: Sized,
//     // {
//     //     actix_web::CustomizeResponder::new(self)
//     // }
// }

// impl Responder for TestError {
//     type Body = ();

//     fn respond_to(self, _: &HttpRequest) -> HttpResponse<Self::Body> {

//         HttpResponse::

//     }
//     // fn into_response(self) -> Response {
//     //     println!("error :{:?}", self);
//     //     (StatusCode::INTERNAL_SERVER_ERROR, "Something went wrong").into_response()
//     // }
// }



use std::{
    convert::Infallible,
    pin::Pin,
    task::{Context, Poll},
};

use actix_web::{
    body::{BodySize, BoxBody, MessageBody},
    get,
    http::{Error as ActixHttpError, StatusCode},
    web::{self, get, Bytes, BytesMut},
    App, Handler, HttpResponse, HttpServer, Responder, ResponseError,
};
use dotenv::dotenv;
use serde::Deserialize;
use test::{get_data, HelloParams, TestError};

mod test;

async fn index() -> HttpResponse {
    HttpResponse::Ok().body("Hello")
}

// #[get("/hello/{param1}")]
// async fn data(params: web::Path<HelloParams>) -> impl Responder {
//     HttpResponse::Ok().body("Hello")
// }

#[get("/hello/{param1}")]
async fn data(params: web::Path<HelloParams>) -> TestError {
    //Result<HttpResponse, TestError> {
    // Ok(HttpResponse::Ok().json("HJM"))
    // Err(TestError::UnKnown)
    TestError::UnKnown
}

#[derive(Deserialize)]
pub struct ErrorBody {
    code: String,
    message: String,
    description: String,
}

// use core::ops::arith::Add;

impl MessageBody for ErrorBody {
    // impl<ErrorBody> MessageBody for &mut ErrorBody
    //     where
    //     ErrorBody: MessageBody + Unpin + ?Sized,
    //         {
    type Error = String;

    fn size(&self) -> BodySize {
        //actix_web::body::BodySize {
        // BodySize::None
        // BodySize::Sized(((self.code.len()+ self.message.len()+self.description.len())).try_into().unwrap())
        // Add((**self).code.size() , (**self).message.size())// ,(**self).description.size())
        self.size()
        // BodySize::Sized(self.len() as u64)

        // match *self {}
    }

    fn poll_next(
        self: std::pin::Pin<&mut Self>,
        cx: &mut std::task::Context<'_>,
    ) -> std::task::Poll<Option<Result<web::Bytes, Self::Error>>> {
        Pin::new(self.get_mut()).poll_next(cx)
        // match *self {}
        // Pin::new(&mut **self).poll_next(cx)
        // let chunk = Bytes(self.code.into_bytes() + &self.message.into_bytes() + &self.description.into_bytes());
        // std::task::Poll::Ready(Some(Ok(chunk)))
    }

    fn try_into_bytes(self) -> Result<Bytes, Self>
    where
        Self: Sized,
    {
        Err(self)
    }

    fn boxed(self) -> BoxBody
    where
        Self: Sized + 'static,
    {
        BoxBody::new(self)
    }

    // fn boxed(self) -> BoxBody
    // where
    //     Self: Sized + 'static,
    // {
    //     BoxBody::new(self)
    // }
}

impl MessageBody for ErrorBody {
    type Error = Infallible;

    fn size(&self) -> BodySize {
        BodySize::Sized((self.code.len() + self.message.len() + self.description.len()) as u64)
    }

    fn poll_next(
        self: Pin<&mut Self>,
        _cx: &mut Context<'_>,
    ) -> Poll<Option<Result<Bytes, Self::Error>>> {
        let payload_string = self.code.clone() + &self.message + &self.description;
        let payload_bytes = Bytes::from(payload_string);
        Poll::Ready(Some(Ok(payload_bytes)))
    }
}

impl Responder for TestError {
    type Body = ErrorBody;

    fn respond_to(self, _req: &actix_web::HttpRequest) -> HttpResponse<Self::Body> {
        match self {
            // _ => HttpResponse::with_body(
            //     StatusCode::INTERNAL_SERVER_ERROR,
            //     ErrorBody {
            //         code: "1234".to_string(),
            //         message: "Error Message".to_string(),
            //         description: "Error_description".to_string(),
            //     },
            // ),
            _ => HttpResponse::with_body(StatusCode::INTERNAL_SERVER_ERROR, "sxdcfvgb".to_string()),
        }
    }
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    dotenv().ok();
    println!("\n service running...");
    HttpServer::new(|| App::new().route("/", get().to(index)).service(data))
        .bind(("127.0.0.1", 8080))?
        .run()
        .await
}

// .\BLRKEC450366LADM
//  _-UNk7b<8;WN[*

// actix-web = { version = "4.9.0", default-features = false, features = ["cookies", "macros", "compress-gzip", "compress-brotli"] }
// diesel = { version = "2.2.4" , features = ["postgres", "r2d2", "chrono", "uuid"] }
// actix-web = { version = "4.9.0", default-features = false, features = ["cookies", "macros", "compress-gzip", "compress-brotli"] }

fn main() {
    println!("Hello, world!");
}



// [dependencies]
// actix-web = "4.9.0"
// diesel = { version = "2.2.4" , features = ["postgres", "r2d2", "chrono", "uuid"] }
// dotenv = "0.15.0"
// serde = { version = "1.0.210" , features = ["derive"] }
