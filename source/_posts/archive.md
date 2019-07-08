---
title: archive
date: 2019-07-07 21:30:58
tags:
categories: golang源码 archive
---
## archive tar包
tar包实现了tar格式压缩文件的读取，

> common.go

### Varable

```dart

var (
	ErrHeader          = errors.New("archive/tar: invalid tar header")
	ErrWriteTooLong    = errors.New("archive/tar: write too long")
	ErrFieldTooLong    = errors.New("archive/tar: header field too long")
	ErrWriteAfterClose = errors.New("archive/tar: write after close")
	errMissData        = errors.New("archive/tar: sparse file references non-existent data")
	errUnrefData       = errors.New("archive/tar: sparse file contains unreferenced data")
	errWriteHole       = errors.New("archive/tar: write non-NUL byte in sparse hole")
)
```

### Constants

``` golang
const (
	// Type '0' indicates a regular file.
	TypeReg  = '0' 普通文件
	TypeRegA = '\x00' // Deprecated: Use TypeReg instead.

	// Type '1' to '6' are header-only flags and may not have a data body.
	TypeLink    = '1' // Hard link 硬连接
	TypeSymlink = '2' // Symbolic link 符号连接。
	TypeChar    = '3' // Character device node 字符设备节点
	TypeBlock   = '4' // Block device node 块设备节点
	TypeDir     = '5' // Directory 目录
	TypeFifo    = '6' // FIFO node 队列节点

	// Type '7' is reserved.    保留位
	TypeCont = '7'

	// Type 'x' is used by the PAX format to store key-value records that
	// are only relevant to the next file.
	// This package transparently handles these types.
	TypeXHeader = 'x' 扩展头

	// Type 'g' is used by the PAX format to store key-value records that
	// are relevant to all subsequent files.
	// This package only supports parsing and composing such headers,
	// but does not currently support persisting the global state across files.
	TypeXGlobalHeader = 'g'  全局扩展头

	// Type 'S' indicates a sparse file in the GNU format.
	TypeGNUSparse = 'S'  稀疏文件

	// Types 'L' and 'K' are used by the GNU format for a meta file
	// used to store the path or link name for the next file.
	// This package transparently handles these types.
	TypeGNULongName = 'L' 下一个文件记录长文字
	TypeGNULongLink = 'K' 下一个记录具有长文字的文件

// Keywords for PAX extended header records.PAX扩展标头记录的关键字
const (
	paxNone     = "" // Indicates that no PAX key is suitable
	paxPath     = "path"
	paxLinkpath = "linkpath"
	paxSize     = "size"
	paxUid      = "uid"
	paxGid      = "gid"
	paxUname    = "uname"
	paxGname    = "gname"
	paxMtime    = "mtime"
	paxAtime    = "atime"
	paxCtime    = "ctime"   // Removed from later revision of PAX spec, but was valid
	paxCharset  = "charset" // Currently unused
	paxComment  = "comment" // Currently unused

	paxSchilyXattr = "SCHILY.xattr."

	// Keywords for GNU sparse files in a PAX extended header.
	paxGNUSparse          = "GNU.sparse."
	paxGNUSparseNumBlocks = "GNU.sparse.numblocks"
	paxGNUSparseOffset    = "GNU.sparse.offset"
	paxGNUSparseNumBytes  = "GNU.sparse.numbytes"
	paxGNUSparseMap       = "GNU.sparse.map"
	paxGNUSparseName      = "GNU.sparse.name"
	paxGNUSparseMajor     = "GNU.sparse.major"
	paxGNUSparseMinor     = "GNU.sparse.minor"
	paxGNUSparseSize      = "GNU.sparse.size"
	paxGNUSparseRealSize  = "GNU.sparse.realsize"
)

```

### type Header

```
type Header struct {
	// Typeflag is the type of header entry.
	// The zero value is automatically promoted to either TypeReg or TypeDir
	// depending on the presence of a trailing slash in Name.
	Typeflag byte

	Name     string // Name of file entry  记录头域的文件名
	Linkname string // Target name of link (valid for TypeLink or TypeSymlink)

	Size  int64  // Logical file size in bytes  大小
	Mode  int64  // Permission and mode bits 权限和模式
	Uid   int    // User ID of owner 
	Gid   int    // Group ID of owner
	Uname string // User name of owner
	Gname string // Group name of owner

	// If the Format is unspecified, then Writer.WriteHeader rounds ModTime
	// to the nearest second and ignores the AccessTime and ChangeTime fields.
	//
	// To use AccessTime or ChangeTime, specify the Format as PAX or GNU.
	// To use sub-second resolution, specify the Format as PAX.
	ModTime    time.Time // Modification time
	AccessTime time.Time // Access time (requires either PAX or GNU support)
	ChangeTime time.Time // Change time (requires either PAX or GNU support)

	Devmajor int64 // Major device number (valid for TypeChar or TypeBlock)字符设备
	Devminor int64 // Minor device number (valid for TypeChar or TypeBlock)

	// Xattrs stores extended attributes as PAX records under the
	// "SCHILY.xattr." namespace.
	//
	// The following are semantically equivalent:
	//  h.Xattrs[key] = value
	//  h.PAXRecords["SCHILY.xattr."+key] = value
	//
	// When Writer.WriteHeader is called, the contents of Xattrs will take
	// precedence over those in PAXRecords.
	//
	// Deprecated: Use PAXRecords instead.
	Xattrs map[string]string  

	// PAXRecords is a map of PAX extended header records.
	//
	// User-defined records should have keys of the following form:
	//	VENDOR.keyword
	// Where VENDOR is some namespace in all uppercase, and keyword may
	// not contain the '=' character (e.g., "GOLANG.pkg.version").
	// The key and value should be non-empty UTF-8 strings.
	//
	// When Writer.WriteHeader is called, PAX records derived from the
	// other fields in Header take precedence over PAXRecords.
	PAXRecords map[string]string  Header中的其他字段优先于PAXRecords。

	// Format specifies the format of the tar header.
	//
	// This is set by Reader.Next as a best-effort guess at the format.
	// Since the Reader liberally reads some non-compliant files,
	// it is possible for this to be FormatUnknown.
	//
	// If the format is unspecified when Writer.WriteHeader is called,
	// then it uses the first format (in the order of USTAR, PAX, GNU)
	// capable of encoding this Header (see Format).
	Format Format 格式
}

```

####  func endOffset
> func (s sparseEntry) endOffset() int64




















