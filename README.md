use std::{fmt, io::Error as IoError, task::Poll};

use actix_web::{
    body::{BodySize, BoxBody, MessageBody},
    http::StatusCode,
    web::Bytes,
    Error as ActixWebError, HttpResponse, ResponseError,
};
use serde_json::Error as SerdeError;

use super::UserinfoFileResponse;

#[derive(Debug)]
pub enum UserInfoError {
    ActixWebErr(ActixWebError),
    SerdeErr(SerdeError),
    IoErr(IoError),
    FileNotFound
}

type UserinfoResult<T> = Result<T, UserInfoError>;

pub type GetUserinfoResponse = UserinfoResult<HttpResponse>;

pub type GetUserinfoFileResult = UserinfoResult<UserinfoFileResponse>;
pub type GetUserinfoFileResponse = UserinfoResult<HttpResponse<BoxBody>>;

impl fmt::Display for UserInfoError {
    fn fmt(&self, _f: &mut fmt::Formatter<'_>) -> fmt::Result {
        todo!()
    }
}

impl From<ActixWebError> for UserInfoError {
    fn from(value: ActixWebError) -> Self {
        UserInfoError::ActixWebErr(value)
    }
}

impl From<SerdeError> for UserInfoError {
    fn from(value: SerdeError) -> Self {
        UserInfoError::SerdeErr(value)
    }
}

impl From<IoError> for UserInfoError {
    fn from(value: IoError) -> Self {
        UserInfoError::IoErr(value)
    }
}

impl ResponseError for UserInfoError {
    fn status_code(&self) -> StatusCode {
        StatusCode::INTERNAL_SERVER_ERROR
    }

    fn error_response(&self) -> HttpResponse<BoxBody> {
        match self {
            UserInfoError::ActixWebErr(_error) => HttpResponse::NotFound()
                .content_type("application/json; charset=utf-8")
                .json({}),
            UserInfoError::FileNotFound => HttpResponse::InternalServerError()
                .content_type("application/json; charset=utf-8")
                .json({}),
            UserInfoError::SerdeErr(_error) => HttpResponse::InternalServerError()
                .content_type("application/json; charset=utf-8")
                .json({}),
            UserInfoError::IoErr(_error) => HttpResponse::InternalServerError()
                .content_type("application/json; charset=utf-8")
                .json({}),
        }
    }
}

impl MessageBody for UserinfoFileResponse {
    type Error = String;

    fn size(&self) -> actix_web::body::BodySize {
        BodySize::Sized((self.id.len() + self.name.len()) as u64)
    }

    fn poll_next(
        self: std::pin::Pin<&mut Self>,
        _cx: &mut std::task::Context<'_>,
    ) -> std::task::Poll<Option<Result<actix_web::web::Bytes, Self::Error>>> {
        let payload_string = self.id.clone() + &self.name;
        let payload_bytes = Bytes::from(payload_string);
        Poll::Ready(Some(Ok(payload_bytes)))
    }
}
